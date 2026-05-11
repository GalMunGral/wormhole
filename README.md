# Wormhole

Split SOCKS5 proxy in Python.

```
YOU <--SOCKS5--> R (Rosen, local) <--custom--> E (Einstein, remote) <--> destination
```

```
YOU ---- VER (x05) ----------->> R
YOU ---- NMETHODS (1) -------->> R
YOU ---- METHODS (1-255) ----->> R
YOU <<-- VER (x05) ------------- R
YOU <<-- METHOD (x00) ---------- R
 |                               | 
YOU ---- VER (x05) ----------->> R
YOU ---- CMD (x01/x02/x03) --->> R
 |                               |
 |                       [if x01 CONNECT]
 |                               |
 |                               R ---- SECRET ("Minkowski") -->> E
 |                               R <<-- REP (0x00) -------------- E
 |                               |                                |
 |                        [if successful]                         |
 |                               |                                |
YOU ---- RSV (x00) ----------->> R                                |
YOU ---- ATYP (x01/x03/x04) -->> R ---- ATYP (x01/x03/x04) ---->> E
YOU ---- DST.ADDR (N) -------->> R ---- DST.ADDR (N) ---------->> E
YOU ---- DST.PORT (2) -------->> R ---- DST.PORT (2) ---------->> E
 |                               |                                E <<-- [handshake] -->> ? 
YOU <<-- VER (x05) ------------- R                                |
YOU <<-- REP (x00/x0X) --------- R <<-- REP (x00/x0X) ----------- E
YOU <<-- RSV (x00) ------------- R                                |
YOU <<-- ATYP (x01) ------------ R                                |
YOU <<-- BND.ADDR (4) ---------- R <<-- BND.ADDR (4) ------------ E
YOU <<-- BND.PORT (2) ---------- R <<-- BND.PORT (2) ------------ E
```

## Usage

**Remote (Einstein):**
```bash
python3 Einstein.py --listen 443
# or install as a systemd service:
bash start.sh  # choose 'einstein'
```

**Local (Rosen):**
```bash
python3 Rosen.py --host <einstein-ip> --port 443 --listen 1080 [--verbose]
```

Point any SOCKS5 client at `127.0.0.1:1080`.