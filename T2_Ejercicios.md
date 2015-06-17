## Ejercicios de clase Tema 2


#### T2.1 Calcular la disponibilidad del sistema si tenemos dos réplicas de cada elemento (en total 3 elementos en cada subsistema)

Partiendo de la tabla de componentes inicial:

| COMPONENT     | AVAILABILITY  |
|---------------|---------------|
| Web           | 85%           |
| Application   | 90%           |
| Database      | 99.9%         |
| DNS           | 98%           |
| Firewall      | 85%           |
| Switch        | 99%           |
| Data Center   | 99.99%        |
| ISP           | 95%           |

    Primera réplica de todos los componentes:
    ---------------------------------------------------
    Web =  85% + (1 - 85%) * 85% = 97.75%
    Application = 90% + (1 - 90%) * 90% = 99%
    Database = 99.9% + (1 - 99.9%) * 99.9% = 99.9999%
    DNS = 98% + (1 - 98%) * 98% = 99.96%
    Firewall = 85% + (1 - 85%) * 85% = 97.75%
    Switch = 99% + (1 - 99%) * 99% = 99.99%
    ISP = 95% + (1 - 95%) * 95% = 99.75%

    Segunda réplica de todos los componentes:
    ---------------------------------------------------
    Web =  97.75% + (1 - 97.75%) * 97.75% = 99.95%
    Application = 99% + (1 - 99%) * 99% = 99.99%
    Database = 99.9999% + (1 - 99.9999%) * 99.9999% = 100%
    DNS = 99.96% + (1 - 99.96%) * 99.96% = 99.9999%
    Firewall = 97.75% + (1 - 97.75%) * 97.75% = 99.95%
    Switch = 99.99% + (1 - 99.99%) * 99.99% = 99.9999%
    ISP = 99.75% + (1 - 99.75%) * 99.75% = 99.9994%

#### T2.2 Buscar _frameworks_ y librerías para diferentes lenguajes que permitan hacer aplicaciones altamente disponibles con relativa facilidad. Como ejemplo, examina [PM2](https://github.com/Unitech/pm2 "PM2) que sirve para administrar clústeres de NodeJS.
