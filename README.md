#include <mcp_can.h>
#include <SPI.h>

MCP_CAN CAN0(10);
byte data[8] = {0, 1, 2, 3, 4, 5, 6, 7};

void setup() {
  Serial.begin(115200);
  if (CAN0.begin(MCP_ANY, CAN_500KBPS, MCP_8MHZ) == CAN_OK)
    Serial.println("CAN OK");
  else
    Serial.println("CAN FAIL");
  CAN0.setMode(MCP_NORMAL);
}

void loop() {
  data[0]++;  // ตัวนับ
  byte r = CAN0.sendMsgBuf(0x100, 0, 8, data);
  Serial.println(r == CAN_OK ? "Sent" : "Send error");
  delay(1000);
}




#include <mcp_can.h>
#include <SPI.h>

MCP_CAN CAN0(10);
#define CAN_INT 2
long unsigned int rxId;
unsigned char len = 0;
unsigned char rxBuf[8];

void setup() {
  Serial.begin(115200);
  if (CAN0.begin(MCP_ANY, CAN_500KBPS, MCP_8MHZ) == CAN_OK)
    Serial.println("CAN OK");
  else
    Serial.println("CAN FAIL");
  CAN0.setMode(MCP_NORMAL);
  pinMode(CAN_INT, INPUT);
}

void loop() {
  if (!digitalRead(CAN_INT)) {
    CAN0.readMsgBuf(&rxId, &len, rxBuf);
    Serial.print("ID: 0x");
    Serial.print(rxId, HEX);
    Serial.print(" Data: ");
    for (byte i = 0; i < len; i++) {
      Serial.print(rxBuf[i]);
      Serial.print(" ");
    }
    Serial.println();
  }
}
