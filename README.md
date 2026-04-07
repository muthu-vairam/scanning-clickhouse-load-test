# scanning-clickhouse-load-test
Load test jmeter scripts for scanning team


1) Download and Install Jmeter
2) Download and install clickhouse driver files


How to run jmx files (from local)
HEAP="-Xms10g -Xmx15g" JVM_ARGS="-XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:+HeapDumpOnOutOfMemoryError" jmeter -n -t "/<path_to_file>/clickhouse_http_default.jmx" -l "$HOME/jmeter-tests/http_results.jtl" -j "$HOME/jmeter-tests/jmeter-http.log" -DBATCH_FILE="$HOME/jmeter-tests/source_ips.tsv" -DBATCH_LIMIT=500 -JTHREADS=10 -JRAMP=2 -JLOOPS=2 -Jjmeter.save.saveservice.autoflush=true -Lorg.apache.jmeter=INFO -LROOT=WARN
