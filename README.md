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

`stages.js` файлыг тусад нь ажиллуулж, ачааллыг 30 секундэд 5 VU, 1 минутанд 30 VU, 30 секундэд 100 VU болгон өсгөж, эцэст нь 30 секундэд 0 VU болгон буулгасан. Тест 2 минут 30 секунд үргэлжилж, хамгийн ихдээ 100 VU хүрсэн.

- `http_req_duration` average: 156.57 ms
- `http_req_duration` p90: 237.75 ms
- `http_req_duration` p95: 246.99 ms
- `http_reqs`: 6,916 нийт, 45.948689/s
- `http_req_failed`: 0.00% (0 / 6,916)
- Checks: 3,458 / 3,458 амжилттай (100.00%)

## SLO / Threshold

Baseline p95 нь 232.78 ms байсан тул 1.5 дахин нөөц тооцож `232.78 × 1.5 = 349.17 ms` SLO сонгосон. Энэ нь бодит baseline хэмжилтэд үндэслэсэн бөгөөд хэвийн хэлбэлзэлд хангалттай зай үлдээнэ.

- `http_req_duration`: `p(95)<349.17`
- `http_req_failed`: `rate<0.01`

## PASS result

`threshold-pass.js` тестэд `p(95)<349.17` болон `rate<0.01` threshold хоёулаа PASS болсон. Бодит p95 нь 235.84 ms, error rate нь 0.00% (0 / 230) байв.

## FAIL result

`threshold-fail.js` тестэд зориудаар хатууруулсан `p(95)<50` threshold FAIL болсон. Бодит p95 нь 234.47 ms тул 50 ms босгыг хангаагүй, харин error-rate threshold нь 0.00% (0 / 222)-оор PASS болсон.

## Screenshots

- [PASS threshold output](screenshots/threshold-pass.png)
- [FAIL threshold output](screenshots/threshold-fail.png)

## Дүгнэлт

1. 5 VU-ийн 1 минутын тестэд p95 нь 233.93 ms, throughput нь 7.616449 request/s, error rate нь 0.00% байв.
2. 30 VU-д p95 236.83 ms болж, 5 VU-тэй харьцуулахад 2.90 ms-ээр өссөн.
3. 100 VU-д p95 243.54 ms болж, 5 VU-ийн хэмжилтээс 9.61 ms-ээр өндөр гарсан.
4. Иймээс энэ хэмжилтэд ачаалал өсөхөд latency бага хэмжээгээр нэмэгдэх хандлага ажиглагдсан.
5. Throughput нь 5 VU-ийн 7.616449 request/s-ээс 30 VU-д 45.377982 request/s, 100 VU-д 150.103075 request/s болж өссөн.
6. Гурван тогтмол VU тестэд error rate 0.00% хэвээр байсан тул энэ ачааллын хүрээнд HTTP хүсэлтүүд амжилттай хариулав.
7. Stages test-д p95 246.99 ms, error rate 0.00%, хамгийн их ачаалал 100 VU байсан нь ачаалал өөрчлөгдөх үед ч алдаа гараагүйг харуулсан.
8. Baseline p95 232.78 ms-д үндэслэн сонгосон 349.17 ms SLO-г PASS test-ийн 235.84 ms p95 хангасан.
9. Харин 50 ms-ийн зориудаар хатуу FAIL threshold нь бодит 234.47 ms p95-д хангагдаагүй бөгөөд latency SLO-оос давбал хэрэглэгчийн хариу хүлээх хугацаа муудна гэдгийг баталсан.
