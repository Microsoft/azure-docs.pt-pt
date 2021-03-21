---
title: Azure SQL Fusos horários geridos por instâncias
description: Conheça as especificidades do fuso horário do Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: b4ba5f50f5c66b404ec76128a828a1060328f81c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690996"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Fusos horários em Azure SQL Gestded Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

O Tempo Universal Coordenado (UTC) é o fuso horário recomendado para o nível de dados das soluções de nuvem. A Azure SQL Managed Instance também oferece uma escolha de fusos horários para atender às necessidades das aplicações existentes que armazenam valores de data e hora e funções de data e hora de chamada com um contexto implícito de um fuso horário específico.

Funções T-SQL como [GETDATE()](/sql/t-sql/functions/getdate-transact-sql) ou código CLR observam o fuso horário definido no nível de instância. Os trabalhos do SqL Server Agent também seguem os horários de acordo com o fuso horário do caso.

  > [!NOTE]
  > A Base de Dados Azure SQL não suporta as definições do fuso horário; segue sempre a UTC. Utilize [o FUSO HORÁRIO](/sql/t-sql/queries/at-time-zone-transact-sql) na Base de Dados SQL se precisar de interpretar informações de data e hora num fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários suportados

Um conjunto de fusos horários suportados é herdado do sistema operativo subjacente à instância gerida. É regularmente atualizado para obter novas definições de fuso horário e refletir alterações nas existentes.

[A política de alterações do horário/fuso horário](/troubleshoot/windows-client/system-management-components/daylight-saving-time-help-support) de verão garante a precisão histórica a partir de 2010.

Uma lista com nomes dos fusos horários suportados é exposta através da [vista sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) do sistema.

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de um caso gerido só pode ser definido durante a criação de exemplo. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de um caso gerido existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Desacorda o fuso horário através do portal Azure

Quando introduzir parâmetros para uma nova instância, selecione um fuso horário da lista de fusos horários suportados.
  
![Definição de um fuso horário durante a criação de exemplos](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Especifique a propriedade timezoneId no seu [modelo de Gestor de Recursos](./create-template-quickstart.md) para definir o fuso horário durante a criação do exemplo.

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

Se não for especificado, o fuso horário é definido para UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verifique o fuso horário de uma instância

A função [CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) devolve um nome de visualização do fuso horário do caso.

## <a name="cross-feature-considerations"></a>Considerações de cross-feature

### <a name="restore-and-import"></a>Restaurar e importar

Pode restaurar um ficheiro de backup ou importar dados para uma instância gerida a partir de uma instância ou de um servidor com diferentes configurações de fuso horário. Certifique-se de que o faz com cuidado. Analise o comportamento da aplicação e os resultados das consultas e relatórios, tal como quando transfere dados entre duas instâncias do SQL Server com diferentes definições de fuso horário.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Quando se realiza um restauro pontual, o tempo para restaurar é interpretado como tempo UTC. Desta forma, evitam-se quaisquer ambiguidades devido ao horário de verão e às suas potenciais alterações.

### <a name="auto-failover-groups"></a>Grupos de ativação pós-falha automática

Usar o mesmo fuso horário através de uma instância primária e secundária num grupo de failover não é aplicado, mas recomendamos vivamente.

  >[!WARNING]
  > Recomendamos vivamente que utilize o mesmo fuso horário para a instância primária e secundária num grupo de failover. Devido a certos casos de uso raro que mantêm o mesmo fuso horário em casos primários e secundários não é aplicado. É importante entender que no caso de falha manual ou automática, a instância secundária irá manter o seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário do caso gerido existente não pode ser alterado. Como uma solução alternativa, crie uma nova instância gerida com o fuso horário adequado e, em seguida, execute uma cópia de segurança manual e restaure, ou o que recomendamos, realizar uma [restauração pontual transversal](./point-in-time-restore.md?tabs=azure-portal#restore-an-existing-database).
- Os processos externos lançados a partir dos trabalhos do SqL Server Agent não observam o fuso horário do caso.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários suportados

| **ID do fuso horário** | **Nome do visor do fuso horário** |
| --- | --- |
| Hora padrão da linha de data | (UTC-12:00) Linha internacional de data oeste |
| UTC-11 | (UTC-11:00) Tempo Universal Coordenado-11 |
| Tempo Padrão Aleuta | (UTC-10:00) Ilhas Aleutas |
| Hora Padrão havaiana | (UTC-10:00) Havaí |
| Hora Padrão marquesas | (UTC-09:30) Ilhas Marquesas |
| Horário padrão do Alasca | (UTC-09:00) Alasca |
| UTC-09 | (UTC-09:00) Tempo Universal Coordenado-09 |
| Horário padrão do Pacífico (México) | (UTC-08:00) Baja Califórnia |
| UTC-08 | (UTC-08:00) Tempo Universal Coordenado-08 |
| Horário padrão do Pacífico | (UTC-08:00) Hora do Pacífico (EUA & Canadá) |
| Tempo Padrão da Montanha dos EUA | (UTC-07:00) Arizona |
| Tempo Padrão de Montanha (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Tempo Padrão da Montanha | (UTC-07:00) Tempo de Montanha (EUA & Canadá) |
| Tempo Padrão da América Central | (UTC-06:00) América Central |
| Tempo Padrão Central | (UTC-06:00) Tempo Central (EUA & Canadá) |
| Hora padrão da ilha de Páscoa | (UTC-06:00) Ilha de Páscoa |
| Tempo Padrão Central (México) | (UTC-06:00) Guadalajara, Cidade do México, Monterrey |
| Tempo Padrão Central do Canadá | (UTC-06:00) Rio Saskatchewan |
| Hora Padrão SA Pacific | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Horário padrão oriental (México) | (UTC-05:00) Rio Chetumal |
| Horário padrão oriental | (UTC-05:00) Horário oriental (EUA & Canadá) |
| Hora Padrão do Haiti | (UTC-05:00) Haiti |
| Hora Padrão de Cuba | (UTC-05:00) Havana |
| Tempo Padrão Oriental dos EUA | (UTC-05:00) Indiana (Leste) |
| Tempo Padrão turcos e caicos | (UTC-05:00) Turcos e Caicos |
| Hora Padrão do Paraguai | (UTC-04:00) Assunção |
| Horário Padrão atlântico | (UTC-04:00) Hora do Atlântico (Canadá) |
| Hora Padrão da Venezuela | (UTC-04:00) Caracas |
| Horário padrão brasileiro central | (UTC-04:00) Rio Cuiaba |
| Hora Padrão Do Oeste da SA | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Tempo Padrão Pacific SA | (UTC-04:00) Santiago |
| Tempo Padrão da Terra Nova | (UTC-03:30) Terra Nova |
| Hora Padrão de Tocantins | (UTC-03:00) Araguaina |
| E. Horário padrão da América do Sul | (UTC-03:00) Brasília |
| Tempo Padrão do Leste da SA | (UTC-03:00) Cayenne |
| Tempo Padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Tempo Padrão da Gronelândia | (UTC-03:00) Gronelândia |
| Tempo Padrão montevidéu | (UTC-03:00) Montevidéu |
| Tempo Padrão de Magallanes | (UTC-03:00) Punta Arenas |
| Hora Padrão de Saint Pierre | (UTC-03:00) São Pedro e Miquelon |
| Horário padrão da Bahia | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Tempo Universal Coordenado-02 |
| hora padrão Mid-Atlantic | (UTC-02:00) Mid-Atlantic - Velho |
| Hora Padrão dos Açores | (UTC-01:00) Açores |
| Tempo Padrão de Cabo Verde | (UTC-01:00) Cabo Verde é. |
| UTC | (UTC) Hora Universal Coordenada |
| Hora Padrão GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Hora Padrão de Greenwich | (UTC+00:00) Monróvia |
| W. Tempo Padrão da Europa | (UTC+01:00) Amesterdão, Berlim, Berna, Roma, Estocolmo, Viena |
| Horário padrão da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Tempo Padrão romance | (UTC+01:00) Bruxelas, Copenhague, Madrid, Paris |
| Tempo Padrão de Marrocos | (UTC+01:00) Casablanca |
| Horário padrão são-tomense | (UTC+01:00) São Tomé |
| Horário padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Tempo Padrão da África Central | (UTC+01:00) África Central Ocidental |
| Tempo Padrão da Jordânia | (UTC+02:00) Amã |
| Tempo Padrão GTB | (UTC+02:00) Atenas |
| Hora Padrão do Médio Oriente | (UTC+02:00) Beirute |
| Hora Padrão do Egito | (UTC+02:00) Cairo |
| E. Tempo Padrão da Europa | (UTC+02:00) Rio Chisinau |
| Tempo Padrão da Síria | (UTC+02:00) Damasco |
| Tempo Padrão da Cisjordânia | (UTC+02:00) Gaza |
| Hora Padrão da África do Sul | (UTC+02:00) Harare |
| Tempo Padrão FLE | (UTC+02:00) Helsínquia, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Tempo Padrão de Israel | (UTC+02:00) Jerusalém |
| Tempo Padrão de Kaliningrado | (UTC+02:00) Kaliningrado |
| Tempo Padrão do Sudão | (UTC+02:00) Cartum |
| Hora Padrão da Líbia | (UTC+02:00) Trípoli |
| Tempo Padrão da Namíbia | (UTC+02:00) Windhoek |
| Hora padrão árabe | (UTC+03:00) Bagdad |
| Tempo Padrão da Turquia | (UTC+03:00) Istambul |
| Hora Padrão Árabe | (UTC+03:00) Kuwait |
| Hora Padrão da Bielorrússia | (UTC+03:00) Rio Minsk |
| Tempo Padrão Russo | (UTC+03:00) Moscovo |
| E. Hora Padrão de África | (UTC+03:00) Nairobi |
| Tempo Padrão do Irão | (UTC+03:30) Teerão |
| Hora Padrão da Arábia | (UTC+04:00) Abu Dhabi |
| Hora Padrão de Astrakhan | (UTC+04:00) Astrakhan |
| Hora Padrão do Azerbaijão | (UTC+04:00) Rio Baku |
| Fuso Horário da Rússia 3 | (UTC+04:00) Izhevsk |
| Hora Padrão da Maurícia | (UTC+04:00) Porto Luís |
| Hora Padrão Saratov | (UTC+04:00) Saratov |
| Tempo Padrão georgiano | (UTC+04:00) Tbilisi |
| Tempo Padrão de Volgograd | (UTC+04:00) Rio Volgogrado |
| Hora Padrão do Cáucaso | (UTC+04:00) Rio Yerevan |
| Horário padrão do Afeganistão | (UTC+04:30) Cabul |
| Hora Padrão da Ásia Ocidental | (UTC+05:00) Ashgabat |
| Hora Padrão de Ekaterinburg | (UTC+05:00) Ekaterinburgo |
| Hora Padrão do Paquistão | (UTC+05:00) Islamabad |
| Tempo Padrão da Índia | (UTC+05:30) Chennai, Kolkata, Mumbai, Nova Deli |
| Tempo Padrão do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Hora Padrão do Nepal | (UTC+05:45) Kathmandu |
| Tempo Padrão da Ásia Central | (UTC+06:00) Astana |
| Horário padrão do Bangladesh | (UTC+06:00) Dhaka |
| Hora Padrão de Omsk | (UTC+06:00) Omsk |
| Tempo Padrão de Myanmar | (UTC+06:30) Yangon (Rangoon) |
| Se Asia Tempo Padrão | (UTC+07:00) Banguecoque, Hanói, Jacarta |
| Hora Padrão Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Hora Padrão da Mongólia | (UTC+07:00) Rio Hovd |
| Horário padrão do Norte da Ásia | (UTC+07:00) Krasnoyarsk |
| N. Tempo Padrão da Ásia Central | (UTC+07:00) Novosibirsk |
| Hora Padrão de Tomsk | (UTC+07:00) Tomsk |
| Tempo Padrão da China | (UTC+08:00) Pequim, Chongqing, Hong Kong, Urumqi |
| Horário padrão do Nordeste da Ásia | (UTC+08:00) Irkutsk |
| Tempo Padrão de Singapura | (UTC+08:00) Kuala Lumpur |
| W. Tempo Padrão da Austrália | (UTC+08:00) Rio Perth |
| Tempo Padrão de Taipei | (UTC+08:00) Taipé |
| Tempo Padrão de Ulaanbaatar | (UTC+08:00) Ulaanbaatar |
| Hora Padrão Aus Central W. | (UTC+08:45) Rio Eucla |
| Tempo Padrão Transbaikal | (UTC+09:00) Chita |
| Hora Padrão de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Horário padrão da Coreia do Norte | (UTC+09:00) Pyongyang |
| Hora Padrão da Coreia | (UTC+09:00) Rio Seul |
| Hora Padrão de Yakutsk | (UTC+09:00) Rio Yakutsk |
| O Cen. Tempo Padrão da Austrália | (UTC+09:30) Adelaide |
| Tempo Padrão Central AUS | (UTC+09:30) Rio Darwin |
| E. Tempo Padrão da Austrália | (UTC+10:00) Rio Brisbane |
| Tempo Padrão AUS Oriental | (UTC+10:00) Camberra |
| Horário padrão do Pacífico Ocidental | (UTC+10:00) Guam |
| Hora Padrão da Tasmânia | (UTC+10:00) Hobart |
| Hora Padrão de Vladivostok | (UTC+10:00) Rio Vladivostok |
| Hora Padrão lord Howe | (UTC+10:30) Ilha Lord Howe |
| Hora Padrão de Bougainville | (UTC+11:00) Ilha Bougainville |
| Fuso Horário da Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora Padrão de Magadan | (UTC+11:00) Magadan |
| Hora Padrão de Norfolk | (UTC+11:00) Ilha Norfolk |
| Hora Padrão de Sakhalin | (UTC+11:00) Sakhalin |
| Horário padrão do Pacífico Central | (UTC+11:00) Salomão é., Nova Caledónia |
| Fuso Horário da Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Horário padrão da Nova Zelândia | (UTC+12:00) Auckland |
| UTC+12 | (UTC+12:00) Tempo Universal Coordenado+12 |
| Tempo Padrão Fiji | (UTC+12:00) Fiji |
| Hora Padrão de Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky - Velho |
| Hora Padrão das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC+13:00) Tempo Universal Coordenado+13 |
| Hora Padrão de Tonga | (UTC+13:00) Nuku'alofa |
| Tempo Padrão de Samoa | (UTC+13:00) Samoa |
| Tempo padrão das ilhas da linha | (UTC+14:00) Ilha Kiritimati |

## <a name="see-also"></a>Ver também 

- [CURRENT_TIMEZONE (Transact-SQL)](/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](/sql/t-sql/functions/current-timezone-id-transact-sql)
- [NO FUSO HORÁRIO (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)