---
title: Fusos horários de instância geridos
description: Conheça as especificidades do fuso horário da Instância Gerida pela Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 37a7f08ff481a04c41adb9bbae9921b985936b4a
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844442"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários em Caso gerido pela base de dados Azure SQL

O Tempo Universal Coordenado (UTC) é o fuso horário recomendado para o nível de dados das soluções em nuvem. A Azure SQL Database Managed Instance também oferece uma escolha de fusos horários para atender às necessidades das aplicações existentes que armazenam valores de data e hora e funções de data e hora de chamada com um contexto implícito de um fuso horário específico.

Funções T-SQL como [getdate()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observam o fuso horário definido no nível de instância. Os trabalhos do Agente de ServidorEs SQL também seguem os horários de acordo com o fuso horário da ocorrência.

  >[!NOTE]
  > A Instância Gerida é a única opção de implementação da Base de Dados Azure SQL que suporta a definição do fuso horário. Outras opções de implementação seguem sempre a UTC.
Utilize [o TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) em bases de dados SQL únicas e reunidas se precisar de interpretar informações de data e hora num fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários suportados

Um conjunto de fusos horários suportados é herdado do sistema operativo subjacente da instância gerida. É regularmente atualizado para obter novas definições de fuso horário e refletir alterações nas existentes.

[Horário de verão/fuso horário muda a política](https://aka.ms/time) garante precisão histórica a partir de 2010.

Uma lista com nomes dos fusos horários suportados é exposta através da vista do sistema [sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de uma instância gerida só pode ser definido durante a criação de exemplos. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de um caso gerido existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Desloque o fuso horário através do portal Azure

Quando introduzir parâmetros para uma nova instância, selecione um fuso horário da lista de fusos horários suportados.
  
![Definição de um fuso horário durante a criação de exemplo](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Especifique a propriedade timezoneId no seu [modelo de Gestor de Recursos](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação de exemplo.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

Uma lista de valores suportados para a propriedade timezoneId está no final deste artigo.

Se não especificado, o fuso horário está definido para UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verifique o fuso horário de um caso

A função [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) devolve um nome de visualização do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações cruzadas

### <a name="restore-and-import"></a>Restaurar e importar

Pode restaurar um ficheiro de backup ou importar dados para uma instância gerida a partir de uma instância ou servidor com diferentes definições de fuso horário. Certifique-se de que o faça com cuidado. Analise o comportamento da aplicação e os resultados das consultas e relatórios, tal como quando transfere dados entre duas instâncias do SQL Server com diferentes definições de fuso horário.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Quando executa uma restauração pontual, o tempo para restaurar é interpretado como tempo UTC. Desta forma, evita-se qualquer ambiguidade devido ao horário de verão e às suas potenciais alterações.

### <a name="auto-failover-groups"></a>Grupos de ativação pós-falha automática

Usar o mesmo fuso horário através de uma instância primária e secundária num grupo de failover não é aplicado, mas recomendamos vivamente.

  >[!WARNING]
  > Recomendamos vivamente que utilize o mesmo fuso horário para a instância primária e secundária num grupo de failover. Devido a certos casos de uso raro que mantêm o mesmo fuso horário em instâncias primárias e secundárias não é aplicado. É importante entender que, no caso de falha manual ou automática, a instância secundária irá manter o seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário da instância gerida existente não pode ser alterado.
- Os processos externos lançados a partir dos postos de trabalho do Agente de Servidores SQL não observam o fuso horário da ocorrência.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários suportados

| **ID do fuso horário** | **Nome de exibição de fuso horário** |
| --- | --- |
| Hora padrão da linha de data | (UTC-12:00) Linha de Data Internacional Oeste |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Tempo Padrão aleutiano | (UTC-10:00) Ilhas Aleutas |
| Tempo Padrão havaiano | (UTC-10:00) Havaí |
| Horário padrão de Marquesas | (UTC-09:30) Ilhas Marquesas |
| Tempo Padrão do Alasca | (UTC-09:00) Alasca |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Horário padrão do Pacífico (México) | (UTC-08:00) Baja Califórnia |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Horário padrão do Pacífico | (UTC-08:00) Tempo do Pacífico (EUA & Canadá) |
| Tempo Padrão da Montanha dos EUA | (UTC-07:00) Arizona |
| Hora padrão da montanha (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora Padrão da Montanha | (UTC-07:00) Tempo de Montanha (EUA & Canadá) |
| Horário padrão da América Central | (UTC-06:00) América Central |
| Horário padrão central | (UTC-06:00) Horário Central (EUA & Canadá) |
| Hora Padrão da Ilha de Páscoa | (UTC-06:00) Ilha de Páscoa |
| Horário central (México) | (UTC-06:00) Guadalajara |
| Horário padrão central do Canadá | (UTC-06:00) Rio Saskatchewan |
| Horário padrão da SA Pacific | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Horário padrão oriental (México) | (UTC-05:00) Chetumal |
| Horário padrão oriental | (UTC-05:00) Tempo Oriental (EUA & Canadá) |
| Hora Padrão do Haiti | (UTC-05:00) Haiti |
| Hora Padrão de Cuba | (UTC-05:00) Havana |
| Horário padrão do leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Horário padrão turcos e caicos | (UTC-05:00) Turcos e Caicos |
| Horário padrão do Paraguai | (UTC-04:00) Assunção |
| Horário padrão do Atlântico | (UTC-04:00) Tempo Atlântico (Canadá) |
| Tempo Padrão da Venezuela | (UTC-04:00) Caracas |
| Horário padrão brasileiro central | (UTC-04:00) Cuiaba |
| Horário padrão da SA Western | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Horário padrão da Pacific SA | (UTC-04:00) Santiago |
| Tempo Padrão da Terra Nova | (UTC-03:30) Terra Nova |
| Tempo Padrão tocantins | (UTC-03:00) Araguaina |
| E. Horário padrão da América do Sul | (UTC-03:00) Brasília |
| Hora padrão da SA Oriental | (UTC-03:00) Cayenne |
| Horário padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Tempo Padrão da Groenlândia | (UTC-03:00) Gronelândia |
| Hora Padrão de Montevidéu | (UTC-03:00) Montevidéu |
| Magallanes Tempo Padrão | (UTC-03:00) Punta Arenas |
| Horário padrão de Saint Pierre | (UTC-03:00) São Pierre e Miquelon |
| Hora Padrão da Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| Horário padrão do Atlântico Médio | (UTC-02:00) Mid-Atlantic - Velho |
| Horário padrão dos Açores | (UTC-01:00) Açores |
| Horário padrão de Cabo Verde | (UTC-01:00) Cabo Verde é. |
| UTC | (UTC) Hora Universal Coordenada |
| Gmt Tempo Padrão | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Hora Padrão de Greenwich | (UTC+00:00) Monróvia |
| W. Hora Padrão da Europa | (UTC+01:00) Amesterdão, Berlim, Berna, Roma, Estocolmo, Viena |
| Horário padrão da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Tempo Padrão romance | (UTC+01:00) Bruxelas, Copenhaga, Madrid, Paris |
| Horário padrão de Marrocos | (UTC+01:00) Casablanca |
| Tempo Padrão de São Tomé | (UTC+01:00) São Tomé |
| Horário padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Horário padrão da África Central | (UTC+01:00) África Central Ocidental |
| Hora Padrão da Jordânia | (UTC+02:00) Amã |
| Tempo Padrão GTB | (UTC+02:00) Atenas |
| Horário padrão do Médio Oriente | (UTC+02:00) Beirute |
| Hora Padrão do Egito | (UTC+02:00) Cairo |
| E. Hora Padrão da Europa | (UTC+02:00) Chisinau |
| Tempo Padrão da Síria | (UTC+02:00) Damasco |
| Hora Padrão da Cisjordânia | (UTC+02:00) Gaza |
| Horário padrão da África do Sul | (UTC+02:00) Harare |
| Tempo padrão FLE | (UTC+02:00) Helsínquia, Kiev, Riga, Sófia, Tallinn, Vilnius |
| Horário padrão de Israel | (UTC+02:00) Jerusalém |
| Horário padrão de Kaliningrado | (UTC+02:00) Kaliningrado |
| Hora Padrão do Sudão | (UTC+02:00) Cartum |
| Tempo Padrão da Líbia | (UTC+02:00) Trípoli |
| Tempo Padrão da Namíbia | (UTC+02:00) Windhoek |
| Tempo Padrão Árabe | (UTC+03:00) Bagdade |
| Hora Padrão da Turquia | (UTC+03:00) Istambul |
| Tempo Padrão Árabe | (UTC+03:00) Kuwait |
| Hora Padrão da Bielorrússia | (UTC+03:00) Rio Minsk |
| Tempo Padrão Russo | (UTC+03:00) Moscovo |
| E. Horário padrão de África | (UTC+03:00) Nairobi |
| Tempo Padrão do Irão | (UTC+03:30) Teerão |
| Tempo Padrão Árabe | (UTC+04:00) Abu Dhabi |
| Tempo Padrão de Astrakhan | (UTC+04:00) Astrakhan |
| Tempo Padrão do Azerbaijão | (UTC+04:00) Rio Baku |
| Zona de Tempo da Rússia 3 | (UTC+04:00) Izhevsk |
| Horário padrão das Maurícias | (UTC+04:00) Port Louis |
| Horário padrão de Saratov | (UTC+04:00) Saratov |
| Horário padrão da Geórgia | (UTC+04:00) Tbilisi |
| Volgogrado Tempo Padrão | (UTC+04:00) Rio Volgogrado |
| Horário padrão do Cáucaso | (UTC+04:00) Rio Yerevan |
| Horário padrão do Afeganistão | (UTC+04:30) Cabul |
| Horário padrão da Ásia Ocidental | (UTC+05:00) Ashgabat |
| Hora Padrão de Ekaterinburg | (UTC+05:00) Ekaterinburgo |
| Hora Padrão do Paquistão | (UTC+05:00) Islamabad |
| Tempo Padrão da Índia | (UTC+05:30) Chennai, Kolkata, Mumbai, Nova Deli |
| Horário padrão do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Hora Padrão do Nepal | (UTC+05:45) Kathmandu |
| Horário padrão da Ásia Central | (UTC+06:00) Astana |
| Hora Padrão do Bangladesh | (UTC+06:00) Dhaka |
| Hora Padrão de Omsk | (UTC+06:00) Rio Omsk |
| Horário padrão de Myanmar | (UTC+06:30) Yangon (Rangoon) |
| Tempo Padrão se Asia | (UTC+07:00) Banguecoque, Hanói, Jacarta |
| Hora Padrão de Altai | (UTC+07:00) Barnaul |
| W. Tempo Padrão da Mongólia | (UTC+07:00) Hovd |
| Horário padrão do Norte da Ásia | (UTC+07:00) Rio Krasnoyarsk |
| N. Horário padrão da Ásia Central | (UTC+07:00) Novosibirsk |
| Hora Padrão de Tomsk | (UTC+07:00) Rio Tomsk |
| Tempo Padrão da China | (UTC+08:00) Pequim, Chongqing, Hong Kong, Urumqi |
| Horário padrão do Leste da Ásia do Norte | (UTC+08:00) Rio Irkutsk |
| Horário padrão de Singapura | (UTC+08:00) Kuala Lumpur |
| W. Tempo Padrão da Austrália | (UTC+08:00) Rio Perth |
| Horário padrão de Taipei | (UTC+08:00) Taipé |
| Tempo Padrão de Ulaanbaatar | (UTC+08:00) Ulaanbaatar |
| Tempo Padrão Aus Central W. | (UTC+08:45) Eucla |
| Tempo Padrão de Transbaikal | (UTC+09:00) Chita |
| Horário padrão de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Hora Padrão da Coreia do Norte | (UTC+09:00) Pyongyang |
| Hora Padrão da Coreia | (UTC+09:00) Rio Seul |
| Horário padrão de Yakutsk | (UTC+09:00) Rio Yakutsk |
| Cen, cen. Tempo Padrão da Austrália | (UTC+09:30) Adelaide |
| Tempo Padrão Central da AUS | (UTC+09:30) Rio Darwin |
| E. Tempo Padrão da Austrália | (UTC+10:00) Rio Brisbane |
| Horário padrão da AUS Oriental | (UTC+10:00) Canberra, Melbourne, Sydney |
| Horário padrão do Pacífico Ocidental | (UTC+10:00) Guam |
| Tempo Padrão da Tasmânia | (UTC+10:00) Hobart |
| Tempo Padrão de Vladivostok | (UTC+10:00) Vladivostok |
| Lord Howe Tempo Padrão | (UTC+10:30) Ilha Lord Howe |
| Horário padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Zona de Tempo da Rússia 10 | (UTC+11:00) Rio Chokurdakh |
| Magadan Standard Time | (UTC+11:00) Rio Magadan |
| Horário padrão de Norfolk | (UTC+11:00) Ilha Norfolk |
| Tempo Padrão de Sakhalin | (UTC+11:00) Sakhalin |
| Horário padrão do Pacífico Central | (UTC+11:00) Salomão Is., Nova Caledonia |
| Zona de Tempo da Rússia 11 | (UTC+12:00) Anadyr |
| Tempo Padrão da Nova Zelândia | (UTC+12:00) Auckland |
| UTC+12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Tempo Padrão de Fiji | (UTC+12:00) Fiji |
| Horário padrão de Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky - Velho |
| Horário padrão das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC+13:00) Tempo Universal Coordenado+13 |
| Hora Padrão de Tonga | (UTC+13:00) Nuku'alofa |
| Tempo Padrão de Samoa | (UTC+13:00) Samoa |
| Tempo padrão das Ilhas line | (UTC+14:00) Ilha Kiritimati |

## <a name="see-also"></a>Consulte também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-id-transact-sql)
- [FUSO HORÁRIO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
