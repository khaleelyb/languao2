import asyncio
import re
from http.server import BaseHTTPRequestHandler
from urllib.parse import urlparse, parse_qs

import edge_tts

VOICES = {
    "ar-SA-HamedNeural", "ar-SA-ZariyahNeural",
    "ar-EG-ShakirNeural", "ar-EG-SalmaNeural",
    "ar-AE-HamdanNeural", "ar-AE-FatimaNeural",
}


async def synth(text, voice, rate):
    out = bytearray()
    async for chunk in edge_tts.Communicate(text, voice, rate=rate).stream():
        if chunk["type"] == "audio":
            out += chunk["data"]
    return bytes(out)


class handler(BaseHTTPRequestHandler):
    def _send(self, code, body=b"", ctype="text/plain", cache=None):
        self.send_response(code)
        self.send_header("Content-Type", ctype)
        self.send_header("Access-Control-Allow-Origin", "*")
        if cache:
            self.send_header("Cache-Control", cache)
        self.end_headers()
        self.wfile.write(body)

    def do_GET(self):
        q = parse_qs(urlparse(self.path).query)
        text = q.get("text", [""])[0].strip()[:300]
        voice = q.get("voice", ["ar-SA-HamedNeural"])[0]
        rate = q.get("rate", ["-20%"])[0]
        if not text or voice not in VOICES or not re.fullmatch(r"[+-]\d{1,2}%", rate):
            return self._send(400, b"bad request")
        try:
            audio = asyncio.run(synth(text, voice, rate))
        except Exception as e:
            return self._send(502, str(e).encode())
        if not audio:
            return self._send(502, b"no audio")
        self._send(200, audio, "audio/mpeg", "public, s-maxage=31536000, immutable")
