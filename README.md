# funnyscript
sysadmin at work means... script in bash!

```bash
{
PARTITION=ochvh
sessiondump --allkeys | grep '.session.ad.last.attr.cn.*icinga_krb5' | cut -d'.' -f1 | xargs -I {} sessiondump --sid {} | grep 'session.server.network.name.default' | grep $PARTITION | cut -d' ' -f3 | sort | uniq
}
```

```bash
{
echo -e "
./.git/COMMIT_EDITMSG
./.git/objects/1e
./.git/objects/1e/05c240cd2455a5f3f4d4828fb1d1d10f7e78e5
./.git/objects/06
./.git/objects/06/16a9c4b530e79ea42d48740d29a705e9975ab3
./.git/objects/a6
./.git/objects/a6/3ac10139db8a71aba37b3131e4a83c6825c18b
./.git/objects/f1
./.git/objects/f1/58609e475fc45491ff41f4a0788179738e4113
./.git/objects/e6
./.git/objects/e6/9de29bb2d1d6434b8b29ae775ad8c2e48c5391
./.git/objects/50
./.git/objects/50/55684dfe33c9faa88d61d3733101509f39b9e8
./.git/refs/heads/master" | while read n;do ls $n;sudo chown pfun:users $n;done;make push
}
```

Playing with AWX API / jq ..
```bash
{
INV=1280
BASE_URL=https://x.x.x
URL=https://x.x.x/api/v2/inventories/$INV/hosts/
TOKEN=x
ID_LIST=""
for i in {1..100};do
STATUS=$(curl --silent -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" -o /dev/null -s -w "%{http_code}\n" ${URL}?page=$i)
echo $STATUS
if [ "$STATUS" == "200" ];then
ID=$(curl --silent -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" -X GET ${URL}?page=$i | jq .results[].related.job_host_summaries)
echo $ID
ID+=" "
ID_LIST+=$ID
else
break
fi
done
for i in $ID_LIST
do
curl --silent -H "Content-Type: application/json" -H "Authorization: Bearer $TOKEN" -X GET ${BASE_URL}${i//\"}?failed=true | jq '.results[] | "\(.summary_fields.host.name): \(.summary_fields.job.status)"' | sort | uniq
done
}
```

awx pt2, find last failed job on hosts from invent
```bash
{
TOKEN=x
URL=x
HEADER_1="Content-Type: application/json"
HEADER_2="Authorization: Bearer $TOKEN"
INVENTORY=1340
TODO=inventories/$INVENTORY/hosts/
METHOD=GET
JOBS=""
for i in {1..100};do
STATUS=$(curl -X $METHOD -H "$HEADER_1" -H "$HEADER_2" ${URL}/${TODO}?page=$i -o /dev/null -s -w "%{http_code}\n")
if [ "$STATUS" == "200" ];then
#ID=$(curl -X $METHOD -H "$HEADER_1" -H "$HEADER_2" ${URL}/${TODO}?page=$i | jq.results.
LAST_SUMMARY_JOB=$(curl -s -X $METHOD -H "$HEADER_1" -H "$HEADER_2" ${URL}/${TODO}?page=$i | jq .results[].related.last_job_host_summary)
LAST_SUMMARY_JOB+=" "
JOBS+=$LAST_SUMMARY_JOB
else
break
fi
done
for i in $JOBS;
do 
curl -s -X $METHOD -H "$HEADER_1" -H "$HEADER_2" x${i//\"} | jq 'select(.failed==true) | .host_name' | sort | uniq  
done
} | wc -w
```



[2025-08-04 15:31:31 +0200] [2485470] [ERROR] Socket error processing request.
Traceback (most recent call last):
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/workers/sync.py", line 134, in handle
    req = next(parser)
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/parser.py", line 42, in __next__
    self.mesg = self.mesg_class(self.cfg, self.unreader, self.source_addr, self.req_count)
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/message.py", line 184, in __init__
    super().__init__(cfg, unreader, peer_addr)
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/message.py", line 55, in __init__
    unused = self.parse(self.unreader)
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/message.py", line 196, in parse
    self.get_data(unreader, buf, stop=True)
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/message.py", line 187, in get_data
    data = unreader.read()
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/unreader.py", line 37, in read
    d = self.chunk()
  File "/inventories/tuxtstmaints/automated-patching/flask_app/env/lib64/python3.6/site-packages/gunicorn/http/unreader.py", line 64, in chunk
    return self.sock.recv(self.mxchunk)
  File "/usr/lib64/python3.6/ssl.py", line 990, in recv
    return self.read(buflen)
  File "/usr/lib64/python3.6/ssl.py", line 867, in read
    return self._sslobj.read(len, buffer)
  File "/usr/lib64/python3.6/ssl.py", line 592, in read
    v = self._sslobj.read(len)
OSError: [Errno 0] Error

