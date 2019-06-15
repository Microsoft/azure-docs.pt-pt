---
title: O Azure SQL Database Managed Instance fusos horários | Documentos da Microsoft"
description: Saiba mais sobre as especificações de fuso horário de instância gerida da base de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: 8499d99ab82fa89062d74c7dc5db5d7dd11e770c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016381"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários na instância gerida da base de dados SQL do Azure

Hora Universal Coordenada (UTC) é o fuso horário recomendado para a camada de dados de soluções na cloud. Instância de gerida de base de dados de SQL do Azure também oferece uma opção de fusos horários para satisfazer as necessidades das aplicações existentes que armazenam valores de data e hora e chamam funções de data e hora com um contexto implícito de um fuso horário específico.

Como o funções T-SQL [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observar o fuso horário que definir na instância do nível. Trabalhos do SQL Server Agent também seguem as agendas de acordo com o fuso horário da instância.

  >[!NOTE]
  > A instância gerida é a opção de implementação apenas de base de dados do SQL do Azure que suporta a definição de fuso horário. Outras opções de implementação seguem sempre UTC.
Uso [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) no únicos e em pool bases de dados SQL se precisar de interpretam as informações de data e hora num fuso horário não UTC.

## <a name="supported-time-zones"></a>Suportado fusos horários

Um conjunto de fusos horários suportados é herdado de sistema operacional subjacente da instância gerida. É atualizada regularmente para obter novas definições de fuso horário e refletir as alterações para os já existentes. 

Uma lista com os nomes das zonas de tempo suportados é exposta por meio da [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) vista de sistema.

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de uma instância gerida pode ser definido durante a criação de instância apenas. O fuso horário padrão é UTC.

  >[!NOTE]
  > Não é possível alterar o fuso horário de uma instância gerida existente.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Definir o fuso horário através do portal do Azure

Ao introduzir parâmetros para uma nova instância, selecione um fuso horário na lista de suportadas fusos horários. 
  
![Definir um fuso horário durante a criação de instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Especifica a propriedade timezoneId em sua [modelo do Resource Manager](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação de instância.

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

É uma lista de valores suportados para a propriedade timezoneId no final deste artigo.

Se não for especificado, é definir o fuso horário UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verifique o fuso horário de uma instância

O [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) função retorna um nome a apresentar do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações sobre várias funcionalidades

### <a name="restore-and-import"></a>Restauro e da importação

Pode restaurar um ficheiro de cópia de segurança ou importar dados para uma instância gerida de uma instância ou um servidor com as definições de fuso horário diferente. Certifique-se fazer isso com cuidado. Analise o comportamento da aplicação e os resultados das consultas e relatórios, tal como quando a transferência de dados entre duas instâncias do SQL Server com as definições de fuso horário diferente.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

Ao efetuar um restauro de ponto no tempo, o tempo de restauro para é interpretado como hora UTC. Esta definição evita qualquer ambiguidade devido ao horário de Verão e as alterações do mesmo potenciais.

### <a name="auto-failover-groups"></a>Grupos de ativação pós-falha automática

Utilizar o mesmo fuso horário entre uma instância primária e secundária de um grupo de ativação pós-falha não é imposta, mas é altamente recomendável.

  >[!WARNING]
  > Recomendamos vivamente que utilize o mesmo fuso horário para a instância primária e secundária de um grupo de ativação pós-falha. Devido a alguns cenários raros, manter o mesmo fuso horário nas instâncias principal e secundárias não é imposta. É importante compreender que, no caso de ativação pós-falha manual ou automática, a instância secundária manterão seu fuso horário original.

## <a name="limitations"></a>Limitações

- Não é possível alterar o fuso horário da instância gerida existente.
- Processos externos iniciados das tarefas do SQL Server Agent não observam o fuso horário da instância.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários suportados

| **ID do fuso horário** | **Nome a apresentar do fuso horário** |
| --- | --- |
| Hora padrão | (UTC-12:00) Oeste da Linha de Data Internacional |
| UTC-11 | (UTC-11:00) Hora Universal Coordenada-11 |
| Ilhas Aleutas | (UTC-10:00) Ilhas Aleutas |
| Hora padrão do Havai | (UTC-10:00) Havai |
| Marquesas Standard Time | (UTC-09:30) Ilhas Marquesas |
| Alaskan Standard Time | (UTC-09:00) Alasca |
| UTC-09 | (UTC-09:00) Hora Universal Coordenada-09 |
| Hora padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Hora Universal Coordenada-08 |
| Hora padrão do Pacífico | (UTC-08:00) Hora do Pacífico (E.U.A. e Canadá) |
| Padrão das montanhosas dos E.U.A. | (UTC-07:00) Arizona |
| Padrão das montanhosas (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora padrão das Montanhas | (UTC-07:00) Hora das Regiões Montanhosas (E.U.A. e Canadá) |
| Hora padrão da América Central | (UTC-06:00) América Central |
| Hora padrão central | (UTC-06:00) Hora Central (E.U.A. e Canadá) |
| Fuso horário da ilha da Páscoa | (UTC-06:00) Ilha da Páscoa |
| Hora padrão do central (México) | (UTC-06:00) Guadalajara, Cidade do México, Monterrey |
| Hora padrão do Canadá Central | (UTC-06:00) Saskatchewan |
| Hora padrão das-Pacífico | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Hora padrão do Leste (México) | (UTC-05:00) Chetumal |
| Hora padrão do leste | (UTC-05:00) Hora do Leste (U.S.A. e Canadá) |
| Fuso horário de Haiti | (UTC-05:00) Haiti |
| Fuso horário de cuba | (UTC-05:00) Havana |
| Hora de padrão do Leste dos EUA | (UTC-05:00) Indiana (Leste) |
| Ilhas Turcas e Caicos fuso horário | (UTC-05:00) Ilhas Turcas e Caicos |
| Hora padrão do Paraguai | (UTC-04:00) Assunção |
| Hora padrão do Atlântico | (UTC-04:00) Hora do Atlântico (Canadá) |
| Venezuela Standard Time | (UTC-04:00) Caracas |
| Hora padrão do Brasil Central | (UTC-04:00) Cuiabá |
| Hora padrão do oeste | (UTC-04:00) Georgetown, La Paz, Manaus, São João |
| Fuso horário do Pacífico das | (UTC-04:00) Santiago |
| Hora padrão da Terra Nova | (UTC-03:30) Terra Nova |
| Fuso horário de Tocantins | (UTC-03:00) Araguaína |
| E. Hora padrão da América do Sul | (UTC-03:00) Brasília |
| Hora padrão do SA leste | (UTC-03:00) Caiena, Fortaleza |
| Hora padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Hora padrão da Gronelândia | (UTC-03:00) Gronelândia |
| Hora padrão de Montevideu | (UTC-03:00) Montevidéu |
| Hora padrão de Magallanes | (UTC-03:00) Punta Arenas |
| Hora padrão de São Pedro | (UTC-03:00) São Pedro e Miquelão |
| Hora padrão da Baía | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Hora Universal Coordenada-02 |
| Hora padrão do Atlântico | (UTC-02:00) Atlântico Central - Antigo |
| Hora padrão dos Açores | (UTC-01:00) Açores |
| Cape Verde Standard Time | (UTC-01:00) Ilhas de Cabo Verde |
| UTC | (UTC) Hora Universal Coordenada |
| Hora padrão de GMT | (UTC+00:00) Dublim, Edimburgo, Lisboa, Londres |
| Greenwich Standard Time | (UTC+00:00) Monróvia, Reiquiavique |
| W. Hora padrão da Europa | (UTC+01:00) Amesterdão, Berlim, Berna, Roma, Estocolmo, Viena |
| Hora padrão da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Hora padrão de Romance | (UTC+01:00) Bruxelas, Copenhaga, Madrid, Paris |
| Hora padrão de Marrocos | (UTC + 01:00) Casablanca |
| Hora padrão de São Tomé | (UTC + 01:00) São Tomé |
| Hora padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Hora padrão da África Central | (UTC+01:00) África Central Ocidental |
| Jordan Standard Time | (UTC+02:00) Amã |
| Hora padrão gt | (UTC+02:00) Atenas, Bucareste |
| Hora padrão do Médio Oriente | (UTC+02:00) Beirute |
| Hora padrão do Egipto | (UTC+02:00) Cairo |
| E. Hora padrão da Europa | (UTC+02:00) Chisinau |
| Hora padrão da Síria | (UTC+02:00) Damasco |
| Hora padrão do Oeste bancária | (UTC+02:00) Gaza, Hebrom |
| Hora padrão da África do Sul | (UTC+02:00) Harare, Pretória |
| Hora padrão da Finlândia | (UTC+02:00) Helsínquia, Kiev, Riga, Sófia, Tallin, Vilnius |
| Israel Standard Time | (UTC+02:00) Jerusalém |
| Hora padrão de Kaliningrado | (UTC+02:00) Kaliningrado |
| Hora Standard do Sudão | (UTC + UTC+02:00) Cartum |
| Hora padrão da Líbia | (UTC+02:00) Trípoli |
| Hora padrão da Namíbia | (UTC+02:00) Windhoek |
| Hora padrão Árabe | (UTC+03:00) Bagdade |
| Hora padrão da Turquia | (UTC + GMT+03:00) Istambul |
| Hora padrão da Arábia Saudita | (UTC+03:00) Kuwait, Riade |
| Hora padrão da Bielorrússia | (UTC+03:00) Minsk |
| Hora padrão da Rússia | (UTC + GMT+03:00) Moscovo, são Petersburgo |
| E. Hora padrão da África | (UTC+03:00) Nairobi |
| Hora padrão do irão | (UTC+03:30) Teerão |
| Hora padrão de Abu Dhabi | (UTC+04:00) Abu Dhabi, Mascate |
| Astrakhan Standard Time | (UTC+04:00) Astrakhan, Ulianovsk |
| Hora padrão do Azerbaijão | (UTC+04:00) Baku |
| Fuso horário Rússia 3 | (UTC+04:00) Izhevsk, Samara |
| Hora padrão da Maurícia | (UTC+04:00) Port Louis |
| Hora padrão de Saratov | (UTC+04:00) Saratov |
| Hora padrão da Geórgia | (UTC+04:00) Tbilisi |
| Hora padrão da Volgogrado | (UTC+04:00) Volgograd |
| Caucasus Standard Time | (UTC+04:00) Erevan |
| Hora padrão do Afeganistão | (UTC+04:30) Cabul |
| Hora padrão da Ásia Oeste | (UTC+05:00) Achgabat, Tashkent |
| Hora padrão de Ecaterimburgo | (UTC+05:00) Ecaterimburgo |
| Hora padrão do Paquistão | (UTC+05:00) Islamabade, Carachi |
| Hora padrão da Índia | (UTC+05:30) Chennai, Calcutá, Mumbai, Nova Deli |
| Sri Lanka Standard Time | (UTC+05:30) Sri Jayawardenepura |
| Hora padrão do Nepal | (UTC+05:45) Catmandu |
| Hora padrão da Ásia Central | (UTC+06:00) Astana |
| Bangladesh Standard Time | (UTC+06:00) Daca |
| Fuso horário de Omsk | (UTC+06:00) Omsk |
| Myanmar Standard Time | (UTC+06:30) Yangon (Rangum) |
| Hora padrão do Sudeste asiático | (UTC+07:00) Banguecoque, Hanói, Jacarta |
| De Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Fuso horário da Mongólia | (UTC+07:00) Hovd |
| Hora padrão do Nordeste da Ásia | (UTC+07:00) Krasnoyarsk |
| N. Hora padrão da Ásia Central | (UTC+07:00) Novosibirsk |
| Fuso horário de Tomsk | (UTC+07:00) Tomsk |
| Hora padrão da China | (UTC+08:00) Pequim, Chongqing, Hong Kong, Urumqi |
| Hora padrão do Nordeste da Ásia | (UTC+08:00) Irkutsk |
| Hora padrão de Singapura | (UTC+08:00) Kuala Lumpur, Singapura |
| W. Hora padrão da Austrália | (UTC+08:00) Perth |
| Hora padrão de Taipé | (UTC+08:00) Taipé |
| Hora padrão de Ulan | (UTC+08:00) Ulan Bator |
| Austrália Central w. Hora padrão | (UTC+08:45) Eucla |
| De Transbaikal | (UTC+09:00) Chita |
| Hora padrão de Tóquio | (UTC+09:00) Osaka, Sapporo, Tóquio |
| Hora padrão da Coreia do Norte | (UTC + UTC+09:00) Pyongyang |
| Hora padrão da Coreia | (UTC+09:00) Seul |
| Hora padrão de Yakutsk | (UTC+09:00) Yakutsk |
| CEN. Hora padrão da Austrália | (UTC+09:30) Adelaide |
| Hora padrão da Austrália Central | (UTC+09:30) Darwin |
| E. Hora padrão da Austrália | (UTC+10:00) Brisbane |
| AUS hora | (UTC+10:00) Camberra, Melbourne, Sydney |
| Hora de padrão do Pacífico Oeste | (UTC+10:00) Guame, Port Moresby |
| Hora padrão da Tasmânia | (UTC+10:00) Hobart |
| Hora padrão de Vladivostoque | (UTC+10:00) Vladivostoque |
| Hora padrão da ilha de Lord Howe | (UTC+10:30) Ilha de Lord Howe |
| Fuso horário de Bougainville | (UTC+11:00) Ilha de Bougainville |
| Fuso horário Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora padrão de Magadã | (UTC+11:00) Magadã |
| Fuso horário de Norfolk | (UTC+11:00) Ilha Norfolk |
| Fuso horário de Sacalina | (UTC+11:00) Sakhalin |
| Hora de padrão do Pacífico Central | (UTC+11:00) Ilhas Salomão, Nova Caledónia |
| Fuso horário Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Hora padrão da Nova Zelândia | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Hora Universal Coordenada+12 |
| Hora padrão das Fiji | (UTC+12:00) Fiji |
| Kamchatka Standard Time | (UTC+12:00) Petropavlovsk-Kamchatsky - Antigo |
| Fuso horário das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC + 13:00) Coordenada Universal tempo + 13 |
| Hora padrão de Tonga | (UTC+13:00) Nucualofa |
| Hora padrão de Samoa | (UTC+13:00) Samoa |
| Hora padrão de ilhas Espórades Equatoriais | (UTC+14:00) Ilha de Kiritimati |

## <a name="see-also"></a>Consulte também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
