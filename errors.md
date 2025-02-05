master01b@master01b:~$ sudo systemctl status etcd
× etcd.service - etcd - highly-available key value store
     Loaded: loaded (/lib/systemd/system/etcd.service; enabled; preset: enabl>     Active: failed (Result: exit-code) since Wed 2025-02-05 20:58:49 CET; 3m>       Docs: https://etcd.io/docs
             man:etcd
    Process: 17920 ExecStart=/usr/bin/etcd $DAEMON_ARGS (code=exited, status=>   Main PID: 17920 (code=exited, status=1/FAILURE)
        CPU: 20ms

Feb 05 20:58:49 master01b etcd[17920]: [WARNING] Deprecated '--logger=capnslo>Feb 05 20:58:49 master01b etcd[17920]: etcd Version: 3.4.23
Feb 05 20:58:49 master01b etcd[17920]: Git SHA: Not provided (use ./build ins>Feb 05 20:58:49 master01b etcd[17920]: Go Version: go1.19.8
Feb 05 20:58:49 master01b etcd[17920]: Go OS/Arch: linux/amd64
Feb 05 20:58:49 master01b etcd[17920]: setting maximum number of CPUs to 8, t>Feb 05 20:58:49 master01b etcd[17920]: error listing data dir: /var/lib/etcd/>Feb 05 20:58:49 master01b systemd[1]: etcd.service: Main process exited, code>Feb 05 20:58:49 master01b systemd[1]: etcd.service: Failed with result 'exit->Feb 05 20:58:49 master01b systemd[1]: Failed to start etcd.service - etcd - h
