1
Wynik docker inspect

PS C:\Users\Student_PL\desktop\breach-checker\frontend> docker inspect --format '{{json .Config.Labels}}' fk-frontend:v1
{"autor":"FK","data":"2026-11-06","maintainer":"NGINX Docker Maintainers <docker-maint@nginx.com>"}

Wynik docker history

PS C:\Users\Student_PL\desktop\breach-checker\frontend> docker history fk-frontend:v1
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
d474ae473f00   5 minutes ago   EXPOSE [80/tcp]                                 0B        buildkit.dockerfile.v0
<missing>      5 minutes ago   RUN /bin/sh -c htmlhint /usr/share/nginx/htm…   4.1kB     buildkit.dockerfile.v0
<missing>      5 minutes ago   COPY . /usr/share/nginx/html # buildkit         32.8kB    buildkit.dockerfile.v0
<missing>      5 minutes ago   RUN /bin/sh -c apk add --no-cache nodejs npm…   99.3MB    buildkit.dockerfile.v0
<missing>      5 minutes ago   LABEL autor=FK data=2026-11-06                  0B        buildkit.dockerfile.v0
<missing>      14 hours ago    RUN /bin/sh -c set -x     && apkArch="$(cat …   52.4MB    buildkit.dockerfile.v0
<missing>      14 hours ago    ENV ACME_VERSION=0.4.1                          0B        buildkit.dockerfile.v0
<missing>      14 hours ago    ENV NJS_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      14 hours ago    ENV NJS_VERSION=1.0.2                           0B        buildkit.dockerfile.v0
<missing>      15 hours ago    CMD ["nginx" "-g" "daemon off;"]                0B        buildkit.dockerfile.v0
<missing>      15 hours ago    STOPSIGNAL SIGQUIT                              0B        buildkit.dockerfile.v0
<missing>      15 hours ago    EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      15 hours ago    ENTRYPOINT ["/docker-entrypoint.sh"]            0B        buildkit.dockerfile.v0
<missing>      15 hours ago    COPY 30-tune-worker-processes.sh /docker-ent…   16.4kB    buildkit.dockerfile.v0
<missing>      15 hours ago    COPY 20-envsubst-on-templates.sh /docker-ent…   12.3kB    buildkit.dockerfile.v0
<missing>      15 hours ago    COPY 15-local-resolvers.envsh /docker-entryp…   12.3kB    buildkit.dockerfile.v0
<missing>      15 hours ago    COPY 10-listen-on-ipv6-by-default.sh /docker…   12.3kB    buildkit.dockerfile.v0
<missing>      15 hours ago    COPY docker-entrypoint.sh / # buildkit          8.19kB    buildkit.dockerfile.v0
<missing>      15 hours ago    RUN /bin/sh -c set -x     && addgroup -g 101…   5.75MB    buildkit.dockerfile.v0
<missing>      15 hours ago    ENV DYNPKG_RELEASE=1                            0B        buildkit.dockerfile.v0
<missing>      15 hours ago    ENV PKG_RELEASE=1                               0B        buildkit.dockerfile.v0
<missing>      15 hours ago    ENV NGINX_VERSION=1.31.8                        0B        buildkit.dockerfile.v0
<missing>      15 hours ago    LABEL maintainer=NGINX Docker Maintainers <d…   0B        buildkit.dockerfile.v0
<missing>      16 hours ago    CMD ["/bin/sh"]                                 0B        buildkit.dockerfile.v0
<missing>      16 hours ago    ADD alpine-minirootfs-3.24.4-x86_64.tar.gz /…   9.09MB    buildkit.dockerfile.v0

Warstwy z obrazu bazowego są dziedziczone automatycznie z instrukcji FROM, a reszta z kolejnych poleceń w Dockerfile (np. COPY, RUN). Rozmiar 0B mają instrukcje, które nie modyfikują systemu plików (np. LABEL, ENV, WORKDIR). Widać moją wartość maintainer, ponieważ nowo zdefiniowane dane nadpisują obraz bazowy.

2
Zawartosc pierwszego build.txt
Fri Nov  6 09:51:56 UTC 2026
Zawartość drugiego build.txt
Fri Nov  6 09:51:56 UTC 2026

Czas w pliku build.txt jest identyczny w obu kontenerach, ponieważ instrukcja RUN date > /usr/share/nginx/html/build.txt wykonuje się tylko raz, podczas budowania obrazu, a nie w momencie uruchamiania kontenerów.

3

linijka root w konfiguracji:
    root   /usr/share/nginx/html;

Pliki kopiujemy do tego katalogu, ponieważ jest to domyślna ścieżka główna (root) serwera Nginx, z której serwer automatycznie pobiera i udostępnia pliki strony internetowej użytkownikom.

4

błędny folder:
PS C:\Users\Student_PL\desktop\breach-checker\frontend> docker exec bec909312869 ls /usr/share/nginx
html
index.html
script.js
style.css

poprawny:
PS C:\Users\Student_PL\desktop\breach-checker\frontend> docker exec bec909312869 ls /usr/share/nginx/html
50x.html
build.txt
index.html

Narzędzie htmlhint sprawdziło lokalny plik źródłowy index.html znajdujący się w katalogu roboczym na hoście, jeszcze przed jego skopiowaniem do wnętrza obrazu przez instrukcję COPY.

5
Untagged: fk-frontend:latest
Untagged: fk-frontend:v2 Deleted: sha256:9954e1634f5d6be7a979416b35cbf6733d19071f34d1880fdabbd98e51cc58a6

Docker rmi usuwa obrazy wtedy gdy:
Usunięto wszystkie tagi wskazujące na dany image id
Żaden inny istniejący obraz nadrzędny ani żaden aktywny/zatrzymany kontener nie korzysta z warstw tego obrazu.




