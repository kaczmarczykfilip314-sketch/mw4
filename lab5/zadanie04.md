1
Odpowiedz health:
status	"ok"

Docker logs backend:
172.17.0.1 - - [13/Nov/2026 11:43:02] "GET /health HTTP/1.1" 200 -

2
Logi:
PS C:\Users\Student_EN\desktop\lab5> docker run --rm --network breach-net healthcheck-probe
backend: OK
PS C:\Users\Student_EN\desktop\lab5> docker run --rm healthcheck-probe
backend: UNREACHABLE

What's next:
    Debug this container error with Gordon → docker ai "help me fix this container error"
PS C:\Users\Student_EN\desktop\lab5> docker stop backend
backend
PS C:\Users\Student_EN\desktop\lab5> docker run --rm --network breach-net healthcheck-probe
backend: UNREACHABLE

Wyjesnienie:
Bez network healthcheck nie polaczy sie z backendem, nie sa w tej samej sieci DNS, wiec nie moze go odpytac.

3
Po restarcie:
PS C:\Users\Student_EN\desktop\lab5> docker restart backend
backend
PS C:\Users\Student_EN\desktop\lab5> docker exec backend cat /tmp/notatka.txt
notatka

Po usunieciu i wywolaniu:
PS C:\Users\Student_EN\desktop\lab5> docker rm -f backend
backend
PS C:\Users\Student_EN\desktop\lab5> docker run -d --network breach-net -p 5000:5000 --name backend hash-checker:latest
118100497a0aad0992e9b84797b08e74fddbc639f081455cd6d06b6d6fb2701a
PS C:\Users\Student_EN\desktop\lab5> docker exec backend cat /tmp/notatka.txt
cat: /tmp/notatka.txt: No such file or directory

Zawartosc historia.txt:
PS C:\Users\Student_EN\desktop\lab5> docker run --rm -v breach-dane:/dane alpine sh -c "date >> /dane/historia.txt; cat /dane/historia.txt"
Fri Nov 13 11:56:07 UTC 2026
Fri Nov 13 11:56:20 UTC 2026

/tmp przetrwal restart poniewaz kontener i zapisany w nim tmp nie zostaly usuniete, tylko zatrzymane. Gdzy kontener zostal usuniety i na nowo uruchomiony z obrazu tmp nie przetrwal bo nie jest czescia obrazu. historia.txt przetrwala usuniecie poniwaz zostala zapisana na osobnym wolumenie wiec usuniecie kontenera nie dotyczy tego pliku. Lista wyciekow przetrwala by docker rm jesli bylaby zapisana w osobnym wolumenie.