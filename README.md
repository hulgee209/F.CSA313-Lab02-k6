# Лаборатори №2 — k6 гүйцэтгэлийн хэмжүүр

Оюутны нэр: Э.Батхүлэг  
Оюутны код: B232270040

## Зорилго

Grafana k6 ашиглан зөвшөөрөгдсөн `https://test.k6.io` target-ийн latency, p90, p95, throughput болон error rate хэмжүүрүүдийг бодитоор хэмжинэ. Ачааллыг 5, 30, 100 VU түвшинд тус тусад нь ажиллуулж харьцуулна.

## Орчин

- Үйлдлийн систем: Windows
- Ажлын хэрэгсэл: Visual Studio Code
- Load-testing хэрэгсэл: Grafana k6
- Target URL: `https://test.k6.io`

## k6 version

```text
k6.exe v2.2.0 (commit/00a9a1b7f5, go1.26.5, windows/amd64)
```

## Baseline

5 VU, 30 секундийн baseline test-ийн бодит үр дүн:

- `http_req_duration` average: 143.04 ms
- `http_req_duration` p90: 230.78 ms
- `http_req_duration` p95: 232.78 ms
- `http_reqs` total: 230
- `http_reqs` rate: 7.49702/s
- `http_req_failed`: 0.00% (0 / 230)

## 5 / 30 / 100 VU-ийн харьцуулалт

| VU | p90 | p95 | Throughput | Error rate |
|---:|---:|---:|---:|---:|
| 5 | 230.90 ms | 233.93 ms | 7.616449/s | 0.00% |
| 30 | 234.54 ms | 236.83 ms | 45.377982/s | 0.00% |
| 100 | 238.22 ms | 243.54 ms | 150.103075/s | 0.00% |

Хүснэгтийн бүх утгыг `results/run-05vu.txt`, `results/run-30vu.txt`, `results/run-100vu.txt` файлуудын бодит k6 summary-оос авсан.

## Stages test

`stages.js` файлыг тусад нь ажиллуулж, ачааллыг 30 секундэд 5 VU, 1 минутанд 30 VU, 30 секундэд 100 VU болгон өсгөж, эцэст нь 30 секундэд 0 VU болгон буулгасан.
