  What Works

  - Transport: HTTPS to ClickHouse HTTP port 8123
  - JMeter plan: HTTP Sampler (Java implementation), POST body is raw SQL
  - URL: https://fraudlogix-dev-poc.coalition.sdm.network:8123/?database=default&default_format=JSON
  - Headers: Content-Type: text/plain; charset=utf-8, Connection: close
  - TLS: Java truststore provided via -Djavax.net.ssl.trustStore=...
  - Response bodies saved to ~/jmeter-tests/http_bodies/resp-*.json for verification

  One‑Time Setup

  - JMX file: /Users/muthu.vairam/Documents/Jmeter/Clickhouuse/clickhouse_http_default.jmx
  - Create output dirs:
      - mkdir -p "$HOME/jmeter-tests/http_bodies"

  How To Change The SQL

  - Edit the JMX at .../clickhouse_http_default.jmx:36 and replace the SQL text under Argument.value with your query.
  - Keep FORMAT JSON at the end of your SQL so assertions/greps are easy.
  - Example body (already present): see .../clickhouse_http_default.jmx:36

  Run Command (Headless)

  - Save results, detailed log, and response bodies, using your truststore:
      - jmeter -n -t "/Users/muthu.vairam/Documents/Jmeter/Clickhouuse/clickhouse_http_default.jmx" -l "$HOME/jmeter-tests/http_results.jtl" -j "$HOME/jmeter-tests/jmeter-http.log" -Djavax.net.ssl.trustStore="$HOME/jmeter-tests/sdm-trust.jks" -Djavax.net.ssl.trustStorePassword=changeit -Djavax.net.ssl.trustStoreType=JKS -Dhttps.protocols=TLSv1.2
        -Djdk.tls.client.protocols=TLSv1.2 -Dhttp.proxyHost= -Dhttp.proxyPort= -Dhttps.proxyHost= -Dhttps.proxyPort= -Djava.net.preferIPv4Stack=true

  Verify It Worked

  - Summary in console/log should show Err: 0 (0.00%)
  - Response body files:
      - ls -1 "$HOME/jmeter-tests/http_bodies"
      - head -n 40 "$HOME/jmeter-tests/http_bodies/resp-1.json"
      - grep -n '"total_rows"' "$HOME/jmeter-tests/http_bodies/"* (or search for any expected field/value)
  - Optional HTML report:
      - jmeter -n -t "/Users/muthu.vairam/Documents/Jmeter/Clickhouuse/clickhouse_http_default.jmx" -l "$HOME/jmeter-tests/http_results.jtl" -e -o "$HOME/jmeter-tests/http_report"

  How To Run Load

  - Quick (GUI): open jmeter, load the JMX, and adjust:
      - Threads (users): .../clickhouse_http_default.jmx:13
      - Loop count: .../clickhouse_http_default.jmx:18
  - Or duplicate the HTTP Sampler in the GUI and chain multiple queries as needed.

  Tips

  - Keep Connection: close and Java implementation to mirror curl/requests behavior.
  - Keep default_format=JSON in the URL and FORMAT JSON in SQL for easy parsing.
  - Now that the truststore works, you can remove the sampler’s “accept all” TLS if you want stricter security; ask and I’ll patch it.

  Troubleshooting

  - No “total_rows” in body:
      - Open the saved JSON at ~/jmeter-tests/http_bodies/resp-*.json and confirm your query. If “data” is empty, check table/database names in the SQL.
  - TLS issues:
      - Add -Djavax.net.debug=ssl,handshake and check $HOME/jmeter-tests/jmeter-http.log for “trustStore”/“TrustAnchor”.

  If you want, I can add a strict JSON assertion to the plan so it automatically fails if, for example, rows < 1 or a field is missing.