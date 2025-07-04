# funnyscript
sysadmin at work means... script in bash!

{
PARTITION=ochvh
sessiondump --allkeys | grep '.session.ad.last.attr.cn.*icinga_krb5' | cut -d'.' -f1 | xargs -I {} sessiondump --sid {} | grep 'session.server.network.name.default' | grep $PARTITION | cut -d' ' -f3 | sort | uniq
}

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
