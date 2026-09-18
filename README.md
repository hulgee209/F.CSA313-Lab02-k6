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

- `http_req_duration` average: 172.4 ms
- `http_req_duration` p90: 277.63 ms
- `http_req_duration` p95: 299.94 ms
- `http_reqs` total: 218
- `http_reqs` rate: 6.995074/s
- `http_req_failed`: 0.00% (0 / 218)

Бүтэн baseline гаралт: [`results/run-baseline-05vu-30s.txt`](results/run-baseline-05vu-30s.txt).

## 5 / 30 / 100 VU-ийн харьцуулалт

| VU | p90 | p95 | Throughput | Error rate |
|---:|---:|---:|---:|---:|
| 5 | 271.09 ms | 286.15 ms | 7.370686/s | 0.00% |
| 30 | 289.02 ms | 305.50 ms | 43.068321/s | 0.00% |
| 100 | 434.41 ms | 564.60 ms | 129.03345/s | 0.00% |

Хүснэгтийн бүх утгыг `results/run-05vu.txt`, `results/run-30vu.txt`, `results/run-100vu.txt` файлуудын бодит k6 summary-оос авсан.

## Stages test

`stages.js` файлыг тусад нь ажиллуулж, ачааллыг 30 секундэд 5 VU, 1 минутанд 30 VU, 30 секундэд 100 VU болгон өсгөж, эцэст нь 30 секундэд 0 VU болгон буулгасан. Тест 2 минут 30 секунд үргэлжилж, хамгийн ихдээ 100 VU хүрсэн.

- `http_req_duration` average: 172.27 ms
- `http_req_duration` p90: 288.01 ms
- `http_req_duration` p95: 350.01 ms
- `http_reqs`: 6,758 нийт, 44.93901/s
- `http_req_failed`: 0.00% (0 / 6,758)
- Checks: 3,379 / 3,379 амжилттай (100.00%)

Бүтэн stages гаралт: [`results/run-stages.txt`](results/run-stages.txt).

## SLO / Threshold

Baseline p95 нь 299.94 ms байсан тул 1.5 дахин нөөц тооцож `299.94 × 1.5 = 449.91 ms` SLO сонгосон. Энэ нь бодит baseline хэмжилтэд үндэслэсэн бөгөөд хэвийн хэлбэлзэлд хангалттай зай үлдээнэ.

- `http_req_duration`: `p(95)<449.91`
- `http_req_failed`: `rate<0.01`

## PASS result

`threshold-pass.js` тестийг 30 VU, 1 минут ажиллуулахад `p(95)<449.91` болон `rate<0.01` threshold хоёулаа PASS болсон. Бодит p95 нь 276.97 ms, error rate нь 0.00% (0 / 2,722) байв.

Бүтэн PASS гаралт: [`results/threshold-pass.txt`](results/threshold-pass.txt).

## FAIL result

`threshold-fail.js` тестийг 30 VU, 1 минут ажиллуулахад зориудаар хатууруулсан `p(95)<50` threshold FAIL болсон. Бодит p95 нь 290.56 ms тул 50 ms босгыг хангаагүй, харин error-rate threshold нь 0.00% (0 / 2,652)-оор PASS болсон.

Бүтэн FAIL гаралт: [`results/threshold-fail.txt`](results/threshold-fail.txt).

## Screenshots

- [Baseline 5 VU / 30 s output](screenshots/baseline-05vu-30s.png)
- [5 VU output](screenshots/run-05vu.png)
- [30 VU output](screenshots/run-30vu.png)
- [100 VU output](screenshots/run-100vu.png)
- [PASS threshold output](screenshots/threshold-pass.png)
- [FAIL threshold output](screenshots/threshold-fail.png)

## Дүгнэлт

1. 5 VU-ийн 1 минутын тестэд p95 нь 286.15 ms, throughput нь 7.370686 request/s, error rate нь 0.00% байв.
2. 30 VU-д p95 305.50 ms болж, 5 VU-тэй харьцуулахад 19.35 ms-ээр өссөн.
3. 100 VU-д p95 564.60 ms болж, 5 VU-ийн хэмжилтээс 278.45 ms-ээр өндөр гарсан.
4. Иймээс энэ хэмжилтэд ачаалал өсөхөд latency, ялангуяа p95, мэдэгдэхүйц нэмэгдэх хандлага ажиглагдсан.
5. Throughput нь 5 VU-ийн 7.370686 request/s-ээс 30 VU-д 43.068321 request/s, 100 VU-д 129.03345 request/s болж өссөн.
6. Гурван тогтмол VU тестэд error rate 0.00% хэвээр байсан тул энэ ачааллын хүрээнд HTTP хүсэлтүүд амжилттай хариулав.
7. Stages test-д p95 350.01 ms, error rate 0.00%, хамгийн их ачаалал 100 VU байсан нь шаталсан ачаалалд ч алдаа гараагүйг харуулсан.
8. Baseline p95 299.94 ms-д үндэслэн сонгосон 449.91 ms SLO-г 30 VU-ийн PASS test-ийн 276.97 ms p95 хангасан.
9. Харин 50 ms-ийн зориудаар хатуу FAIL threshold нь бодит 290.56 ms p95-д хангагдаагүй бөгөөд latency SLO-оос давбал хэрэглэгчийн хариу хүлээх хугацаа муудна гэдгийг баталсан.
