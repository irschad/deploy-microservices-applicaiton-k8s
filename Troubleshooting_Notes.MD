The following issue was observed with adservice deployment: 
```bash
$ kubectl get pods -n microservices
NAME                                     READY   STATUS             RESTARTS      AGE
adservice-74bcf856c4-2q7zg               0/1     CrashLoopBackOff   3 (26s ago)   88s
adservice-74bcf856c4-6qb5p               0/1     CrashLoopBackOff   3 (27s ago)   88s
cartservice-68f8747f94-6929q             1/1     Running            0             82m
cartservice-68f8747f94-wjmsx             1/1     Running            0             82m
checkoutservice-86bdcfbbfc-4q7s4         1/1     Running            0             82m
checkoutservice-86bdcfbbfc-zknjm         1/1     Running            0             82m
currencyservice-6f7fd6989-ghlpn          1/1     Running            0             82m
currencyservice-6f7fd6989-q8zz6          1/1     Running            0             82m
emailservice-bb589dbcd-2mc4m             1/1     Running            0             82m
emailservice-bb589dbcd-dvrr6             1/1     Running            0             82m
frontend-55cf69d447-7w69j                1/1     Running            0             82m
frontend-55cf69d447-qz79r                1/1     Running            0             82m
paymentservice-c5776df6f-l47v4           1/1     Running            0             82m
paymentservice-c5776df6f-vqxdc           1/1     Running            0             82m
productcatalogservice-8d4f48b75-g6shq    1/1     Running            0             82m
productcatalogservice-8d4f48b75-ghvx9    1/1     Running            0             82m
recommendationservice-69459db9c9-lr62l   1/1     Running            0             82m
recommendationservice-69459db9c9-x45pb   1/1     Running            0             82m
redis-cart-8c5bbbccf-cwq9s               1/1     Running            0             82m
redis-cart-8c5bbbccf-zmz6p               1/1     Running            0             82m
shippingservice-5cc877bc4c-2v84q         1/1     Running            0             82m
shippingservice-5cc877bc4c-cm6mr         1/1     Running            0             82m
```
On viewing logs of one of adservice pods,
```bash
$ kubectl logs adservice-74bcf856c4-2q7zg -n microservices
E1224 14:26:11.180430    16 throttler_api.cc:92] GRPC: src/core/lib/security/credentials/google_default/google_default_credentials.cc:351 Could not create google default credentials: {"created":"@1735050371.177801107","description":"Failed to create Google credentials","file":"src/core/lib/security/credentials/google_default/google_default_credentials.cc","file_line":284,"referenced_errors":[{"created":"@1735050371.177802451","description":"creds_path unset","file":"src/core/lib/security/credentials/google_default/google_default_credentials.cc","file_line":229},{"created":"@1735050371.177826740","description":"Failed to load file","file":"src/core/lib/iomgr/load_file.cc","file_line":71,"filename":"/root/.config/gcloud/application_default_credentials.json","referenced_errors":[{"created":"@1735050371.177825650","description":"No such file or directory","errno":2,"file":"src/core/lib/iomgr/load_file.cc","file_line":45,"os_error":"No such file or directory","syscall":"fopen"}]}]}
E1224 14:26:11.180627    16 throttler_api.cc:116] Failed to get Google default credentials
E1224 14:26:11.180927    29 throttler_api.cc:297] Profiler API is not initialized, stop profiling
```
The error indicates that the ad service is attempting to use Google Cloud Profiler but is unable to initialize it due to missing Google credentials. To resolve this, we can disable the Profiler in the service configuration.
Also, the logs indicate that the Profiler API is looking for application_default_credentials.json in /root/.config/gcloud/.
So, we can redirect it to a non-existent path.
Hence, the following is added in adservice deployment: 
```bash
        env:
        - name: ENABLE_PROFILER
          value: "false"
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: "/dev/null"
        - name: ENABLE_STACKDRIVER_PROFILER
          value: "false"
```
After making changes to config and applying,
```bash
$ kubectl apply -f config.yaml -n microservices
deployment.apps/emailservice unchanged
service/emailservice unchanged
deployment.apps/recommendationservice unchanged
service/recommendationservice unchanged
deployment.apps/productcatalogservice unchanged
service/productcatalogservice unchanged
deployment.apps/paymentservice unchanged
service/paymentservice unchanged
deployment.apps/currencyservice unchanged
service/currencyservice unchanged
deployment.apps/shippingservice unchanged
service/shippingservice unchanged
deployment.apps/adservice created
service/adservice unchanged
deployment.apps/cartservice unchanged
service/cartservice unchanged
deployment.apps/redis-cart unchanged
service/redis-cart unchanged
deployment.apps/checkoutservice unchanged
service/checkoutservice unchanged
deployment.apps/frontend unchanged
service/frontend unchanged
```
Checking the pods status:
```bash
$ kubectl get pods -n microservices
NAME                                     READY   STATUS    RESTARTS   AGE
adservice-584d47dcbb-6hznn               0/1     Running   0          7s
adservice-584d47dcbb-86zdj               0/1     Running   0          7s
cartservice-68f8747f94-6929q             1/1     Running   0          90m
cartservice-68f8747f94-wjmsx             1/1     Running   0          90m
checkoutservice-86bdcfbbfc-4q7s4         1/1     Running   0          90m
checkoutservice-86bdcfbbfc-zknjm         1/1     Running   0          90m
currencyservice-6f7fd6989-ghlpn          1/1     Running   0          90m
currencyservice-6f7fd6989-q8zz6          1/1     Running   0          90m
emailservice-bb589dbcd-2mc4m             1/1     Running   0          90m
emailservice-bb589dbcd-dvrr6             1/1     Running   0          90m
frontend-55cf69d447-7w69j                1/1     Running   0          90m
frontend-55cf69d447-qz79r                1/1     Running   0          90m
paymentservice-c5776df6f-l47v4           1/1     Running   0          90m
paymentservice-c5776df6f-vqxdc           1/1     Running   0          90m
productcatalogservice-8d4f48b75-g6shq    1/1     Running   0          90m
productcatalogservice-8d4f48b75-ghvx9    1/1     Running   0          90m
recommendationservice-69459db9c9-lr62l   1/1     Running   0          90m
recommendationservice-69459db9c9-x45pb   1/1     Running   0          90m
redis-cart-8c5bbbccf-cwq9s               1/1     Running   0          90m
redis-cart-8c5bbbccf-zmz6p               1/1     Running   0          90m
shippingservice-5cc877bc4c-2v84q         1/1     Running   0          90m
shippingservice-5cc877bc4c-cm6mr         1/1     Running   0          90m
```
```bash
[ec2-user@ip-10-0-42-244 ~]$ kubectl get pods -n microservices
NAME                                     READY   STATUS    RESTARTS   AGE
adservice-584d47dcbb-6hznn               1/1     Running   0          16s
adservice-584d47dcbb-86zdj               1/1     Running   0          16s
cartservice-68f8747f94-6929q             1/1     Running   0          90m
cartservice-68f8747f94-wjmsx             1/1     Running   0          90m
checkoutservice-86bdcfbbfc-4q7s4         1/1     Running   0          90m
checkoutservice-86bdcfbbfc-zknjm         1/1     Running   0          90m
currencyservice-6f7fd6989-ghlpn          1/1     Running   0          90m
currencyservice-6f7fd6989-q8zz6          1/1     Running   0          90m
emailservice-bb589dbcd-2mc4m             1/1     Running   0          90m
emailservice-bb589dbcd-dvrr6             1/1     Running   0          90m
frontend-55cf69d447-7w69j                1/1     Running   0          90m
frontend-55cf69d447-qz79r                1/1     Running   0          90m
paymentservice-c5776df6f-l47v4           1/1     Running   0          90m
paymentservice-c5776df6f-vqxdc           1/1     Running   0          90m
productcatalogservice-8d4f48b75-g6shq    1/1     Running   0          90m
productcatalogservice-8d4f48b75-ghvx9    1/1     Running   0          90m
recommendationservice-69459db9c9-lr62l   1/1     Running   0          90m
recommendationservice-69459db9c9-x45pb   1/1     Running   0          90m
redis-cart-8c5bbbccf-cwq9s               1/1     Running   0          90m
redis-cart-8c5bbbccf-zmz6p               1/1     Running   0          90m
shippingservice-5cc877bc4c-2v84q         1/1     Running   0          90m
shippingservice-5cc877bc4c-cm6mr         1/1     Running   0          90m
```

Finally, checked services and viewed the application in browser using loadbalancer URL.

