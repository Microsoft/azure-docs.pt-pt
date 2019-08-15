---
title: Instância Gerenciada do Banco de Dados SQL do Azure fusos horários | Microsoft Docs "
description: Saiba mais sobre as especificações de fuso horário do Instância Gerenciada do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a02709ffde144e7bd5e4d05fcd0e07c5d84a15fb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035825"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Fusos horários em Instância Gerenciada do Banco de Dados SQL do Azure

UTC (tempo Universal Coordenado) é o fuso horário recomendado para a camada de dados das soluções de nuvem. O Instância Gerenciada do Banco de Dados SQL do Azure também oferece uma escolha de fusos horários para atender às necessidades de aplicativos existentes que armazenam valores de data e hora e funções de data e hora com um contexto implícito de um fuso horário específico.

Funções T-SQL como [GETDATE ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) ou código CLR observam o fuso horário definido no nível da instância. SQL Server Agent trabalhos também seguem agendas de acordo com o fuso horário da instância.

  >[!NOTE]
  > Instância Gerenciada é a única opção de implantação do banco de dados SQL do Azure que dá suporte à configuração de fuso horário. Outras opções de implantação sempre seguem o UTC.
Use [no fuso horário](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) em bancos de dados SQL únicos e em pool se você precisar interpretar informações de data e hora em um fuso horário não UTC.

## <a name="supported-time-zones"></a>Fusos horários com suporte

Um conjunto de fusos horários com suporte é herdado do sistema operacional subjacente da instância gerenciada. Ele é atualizado regularmente para obter novas definições de fuso horário e refletir as alterações nas existentes.

A [política de horário de verão e alterações de fuso horário](https://aka.ms/time) garante a precisão histórica de 2010 para frente.

Uma lista com nomes dos fusos horários com suporte é exposta por meio da exibição do sistema [Sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) .

## <a name="set-a-time-zone"></a>Definir um fuso horário

Um fuso horário de uma instância gerenciada pode ser definido somente durante a criação da instância. O fuso horário padrão é UTC.

  >[!NOTE]
  > O fuso horário de uma instância gerenciada existente não pode ser alterado.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Definir o fuso horário por meio do portal do Azure

Ao inserir parâmetros para uma nova instância, selecione um fuso horário na lista de fusos horários com suporte.
  
![Definindo um fuso horário durante a criação da instância](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Especifique a propriedade TimeZoneID em seu [modelo do Resource Manager](https://aka.ms/sql-mi-create-arm-posh) para definir o fuso horário durante a criação da instância.

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

Uma lista de valores com suporte para a propriedade TimeZoneID está no final deste artigo.

Se não for especificado, o fuso horário será definido como UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Verificar o fuso horário de uma instância

A função [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) retorna um nome de exibição do fuso horário da instância.

## <a name="cross-feature-considerations"></a>Considerações sobre recursos cruzados

### <a name="restore-and-import"></a>Restaurar e importar

Você pode restaurar um arquivo de backup ou importar dados para uma instância gerenciada de uma instância do ou um servidor com configurações de fuso horário diferentes. Certifique-se de fazer isso com cautela. Analise o comportamento do aplicativo e os resultados das consultas e dos relatórios, assim como quando você transfere dados entre duas instâncias de SQL Server com configurações de fuso horário diferentes.

### <a name="point-in-time-restore"></a>Restauro para um ponto anterior no tempo

<del>Quando você executa uma restauração pontual, o tempo para restaurar é interpretado como hora UTC. Dessa forma, quaisquer ambiguidades devido ao horário de verão e suas possíveis alterações são evitadas.<del>

 >[!WARNING]
  > O comportamento atual não está em linha com a instrução acima, e o tempo para restaurar é interpretado de acordo com o fuso horário da instância gerenciada de origem na qual os backups automáticos de banco de dados são obtidos. Estamos trabalhando para corrigir esse comportamento para interpretar determinado ponto no tempo como hora UTC.

### <a name="auto-failover-groups"></a>Grupos de ativação pós-falha automática

Usar o mesmo fuso horário em uma instância primária e secundária em um grupo de failover não é imposto, mas é altamente recomendável.

  >[!WARNING]
  > É altamente recomendável que você use o mesmo fuso horário para a instância primária e secundária em um grupo de failover. Devido a alguns cenários raros, manter o mesmo fuso horário em instâncias primárias e secundárias não é imposto. É importante entender que, no caso de failover manual ou automático, a instância secundária manterá seu fuso horário original.

## <a name="limitations"></a>Limitações

- O fuso horário da instância gerenciada existente não pode ser alterado.
- Os processos externos iniciados por meio dos trabalhos de SQL Server Agent não observam o fuso horário da instância.

## <a name="known-issues"></a>Problemas conhecidos

Quando a operação de restauração pontual (PITR) é executada, o tempo de restauração é interpretado como por conjunto de zonas de tempo na instância gerenciada na qual os backups automáticos de banco de dados são obtidos, embora a página do portal para PITR sugira que o tempo seja interpretado como UTC.

Exemplo:

Digamos que a instância em que os backups automáticos são obtidos tem fuso horário padrão do leste (UTC-5).
A página do portal para a restauração pontual sugere que a hora em que você está optando por restaurar é hora UTC:

![PITR com a hora local usando o portal](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

No entanto, o tempo para restaurar é realmente interpretado como hora padrão do leste e, neste banco de dados de exemplo específico, será restaurado para o estado às 9h, horário padrão do leste e não hora UTC.

Se você quiser fazer uma restauração pontual em um ponto específico em hora UTC, primeiro Calcule o tempo equivalente no fuso horário da instância de origem e use esse tempo no portal ou no script PowerShell/CLI.

## <a name="list-of-supported-time-zones"></a>Lista de fusos horários com suporte

| **ID do fuso horário** | **Nome de exibição do fuso horário** |
| --- | --- |
| Hora Padrão da Linha de Data | (UTC-12:00) Oeste da Linha de Data Internacional |
| UTC-11 | (UTC-11:00) Hora Universal Coordenada -11 |
| Fuso Horário das Ilhas Aleutas | (UTC-10:00) Ilhas Aleutian |
| Hora Padrão do Havai | (UTC-10:00) Havai |
| Fuso Horário das Ilhas Marquesas | (UTC-09:30) Ilhas Marquesas |
| Hora Padrão do Alasca | (UTC-09:00) Alasca |
| UTC-09 | (UTC-09:00) Hora Universal Coordenada-09 |
| Hora Padrão do Pacífico (México) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Hora Universal Coordenada-08 |
| Hora Padrão do Pacífico | (UTC-08:00) Hora do Pacífico (E.U.A. e Canadá) |
| Hora Padrão das Regiões Montanhosas dos E.U.A. | (UTC-07:00) Arizona |
| Hora Padrão das Regiões Montanhosas (México) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Hora Padrão das Montanhosas | (UTC-07:00) Hora das Regiões Montanhosas (E.U.A. e Canadá) |
| Hora Padrão da América Central | (UTC-06:00) América Central |
| Hora Padrão Central | (UTC-06:00) Hora Central (E.U.A. e Canadá) |
| Fuso Horário da Ilha de Páscoa | (UTC-06:00) Ilha da Páscoa |
| Hora Padrão Central (México) | (UTC-06:00) Guadalajara, Cidade do México, Monterrey |
| Hora Padrão do Canadá Central | (UTC-06:00) Saskatchewan |
| Hora Padrão da AS - Pacífico | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Hora Padrão do Leste (México) | (UTC-05:00) Chetumal |
| Hora Padrão do Leste | (UTC-05:00) Hora do Leste (E.U.A. e Canadá) |
| Fuso Horário de Haiti | (UTC-05:00) Haiti |
| Fuso Horário de Cuba | (UTC-05:00) Havana |
| Hora Padrão do Leste dos E.U.A | (UTC-05:00) Indiana (Leste) |
| Hora oficial turcos e Caicos | (UTC-05:00) Ilhas Turcas e Caicos |
| Hora Padrão do Paraguai | (UTC-04:00) Assunção |
| Hora Padrão do Atlântico | (UTC-04:00) Hora do Atlântico (Canadá) |
| Hora Padrão da Venezuela | (UTC-04:00) Caracas |
| Hora Padrão do Brasil Central | (UTC-04:00) Cuiabá |
| Hora Padrão do Oeste da AS | (UTC-04:00) Georgetown, La Paz, Manaus, São João |
| Hora Padrão do Pacífico da AS | (UTC-04:00) Santiago |
| Hora padrão da Terra Nova | (UTC-03:30) Terra Nova |
| Fuso Horário de Tocantins | (UTC-03:00) Araguaina |
| E. Hora oficial da América do Sul | (UTC-03:00) Brasília |
| Hora Padrão do Leste da AS | (UTC-03:00) Caiena, Fortaleza |
| Hora Padrão da Argentina | (UTC-03:00) Cidade de Buenos Aires |
| Hora Padrão da Gronelândia | (UTC-03:00) Gronelândia |
| Hora Padrão de Montevideu | (UTC-03:00) Montevidéu |
| Hora Padrão de Magallanes | (UTC-03:00) Punta Arenas |
| Fuso Horário de Saint Pierre | (UTC-03:00) São Pedro e Miquelão |
| Hora Padrão da Baía | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Hora Universal Coordenada -02 |
| Hora Padrão do Atlântico Central | (UTC-02:00) Atlântico Central - Antigo |
| Hora Padrão dos Açores | (UTC-01:00) Açores |
| Hora oficial do cabo verde | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Hora Universal Coordenada |
| Hora Padrão de GMT | (UTC+00:00) Dublin, Edimburgo, Lisboa, Londres |
| Hora Padrão de Greenwich | (UTC+00:00) Monróvia, Reiquiavique |
| W. Hora padrão da Europa | (UTC+01:00) Amesterdão, Berlim, Berna, Roma, Estocolmo, Viena |
| Hora Padrão da Europa Central | (UTC+01:00) Belgrado, Bratislava, Budapeste, Liubliana, Praga |
| Hora Padrão de Romance | (UTC+01:00) Bruxelas, Copenhaga, Madrid, Paris |
| Hora Padrão de Marrocos | (UTC+01:00) Casablanca |
| Hora Padrão de São Tomé | (UTC+01:00) São Tomé |
| Hora Padrão da Europa Central | (UTC+01:00) Sarajevo, Skopje, Varsóvia, Zagreb |
| W. Hora oficial da África Central | (UTC+01:00) África Centro Oeste |
| Hora Padrão da Jordânia | (UTC+02:00) Amã |
| Hora Padrão GT | (UTC+02:00) Atenas, Bucareste |
| Hora Padrão do Médio Oriente | (UTC+02:00) Beirute |
| Hora Padrão do Egipto | (UTC+02:00) Cairo |
| E. Hora padrão da Europa | (UTC+02:00) Chisinau |
| Hora Padrão da Síria | (UTC+02:00) Damasco |
| Hora oficial do banco oeste | (UTC+02:00) Gaza, Hebron |
| Hora Padrão da África do Sul | (UTC+02:00) Harare, Pretória |
| Hora Padrão da Finlândia | (UTC+02:00) Helsínquia, Kiev, Riga, Sófia, Tallin, Vilnius |
| Hora padrão de Israel | (UTC+02:00) Jerusalém |
| Hora padrão de Caliningrado | (UTC+02:00) Kaliningrado |
| Hora Standard do Sudão | (UTC+02:00) Cartum |
| Hora Padrão da Líbia | (UTC+02:00) Trípoli |
| Hora Padrão da Namíbia | (UTC+02:00) Windhoek |
| Hora Padrão Árabe | (UTC+03:00) Bagdade |
| Hora Padrão da Turquia | (UTC+03:00) Istambul |
| Hora Padrão da Arábia Saudita | (UTC+03:00) Kuwait, Riade |
| Hora Padrão da Bielorrússia | (UTC+03:00) Minsk |
| Hora oficial russa | (UTC+03:00) Moscovo, São Petersburgo |
| E. Hora padrão da África | (UTC+03:00) Nairobi |
| Hora Padrão do Irão | (UTC+03:30) Teerão |
| Hora Padrão de Abu Dhabi | (UTC+04:00) Abu Dhabi, Mascate |
| Fuso Horário de Astracã | (UTC+04:00) Astrakhan, Ulyanovsk |
| Hora Padrão do Azerbaijão | (UTC+04:00) Baku |
| Zona 3 de tempo da Rússia | (UTC+04:00) Izhevsk, Samara |
| Hora Padrão da Maurícia | (UTC+04:00) Port Louis |
| Hora Padrão de Saratov | (UTC + 04:00) Saratov |
| Hora Padrão da Geórgia | (UTC+04:00) Tbilisi |
| Hora de Volgogrado | (UTC+04:00) Volgogrado |
| Hora Padrão do Cáucaso | (UTC+04:00) Erevan |
| Hora Padrão do Afeganistão | (UTC+04:30) Cabul |
| Hora Padrão da Ásia Oeste | (UTC+05:00) Achgabat, Tashkent |
| Hora padrão de Ecaterimburgo | (UTC+05:00) Ecaterimburgo |
| Hora Padrão do Paquistão | (UTC+05:00) Islamabade, Carachi |
| Hora Padrão da Índia | (UTC+05:30) Chennai, Kolkata (Calcutá), Mumbai, Nova Deli |
| Hora Padrão do Sri Lanka | (UTC+05:30) Sri Jayawardenepura |
| Hora Padrão do Nepal | (UTC+05:45) Catmandu |
| Hora Padrão da Ásia Central | (UTC+06:00) Astana |
| Hora Padrão do Bangladeche | (UTC+06:00) Daca |
| Fuso Horário de Omsk | (UTC + 06:00) Omsk |
| Hora Padrão de Myanmar | (UTC+06:30) Yangon (Rangum) |
| Hora Padrão do Sudeste Asiático | (UTC+07:00) Banguecoque, Hanói, Jacarta |
| Fuso Horário de Altai | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Hora padrão da Mongólia | (UTC+07:00) Hovd­ |
| Hora oficial do norte da Ásia | (UTC+07:00) Krasnoyarsk |
| P. Hora Padrão da Ásia Central | (UTC+07:00) Novosibirsk |
| Fuso Horário de Tomsk | (UTC+07:00) Tomsk |
| Hora Padrão da China | (UTC+08:00) Pequim, Chongqing, Hong Kong, Urumqi |
| Hora padrão do norte da Ásia Oriental | (UTC+08:00) Irkutsk |
| Hora oficial de Cingapura | (UTC+08:00) Kuala Lumpur, Singapura |
| W. Hora padrão da Austrália | (UTC+08:00) Perth |
| Hora Padrão de Taipé | (UTC+08:00) Taipé |
| Hora Padrão de Ulan Bator | (UTC+08:00) Ulan Bator |
| Fuso Horário do Centro-Oeste da Austrália | (UTC+08:45) Eucla |
| Fuso Horário de Transbaikal | (UTC+09:00) Chita |
| Hora Padrão de Tóquio | (UTC+09:00) Osaca, Sapporo, Tóquio |
| Hora Padrão da Coreia do Norte | (UTC+09:00) Pyongyang |
| Hora Padrão da Coreia | (UTC+09:00) Seul |
| Hora padrão de Yakutsk | (UTC+09:00) Yakutsk |
| Central. Hora padrão da Austrália | (UTC+09:30) Adelaide |
| Hora padrão da Austrália Central | (UTC+09:30) Darwin |
| E. Hora padrão da Austrália | (UTC+10:00) Brisbane |
| Hora Padrão da Austrália Oriental | (UTC+10:00) Camberra, Melbourne, Sydney |
| Hora Padrão do Pacífico Oeste | (UTC+10:00) Guame, Port Moresby |
| Hora Padrão da Tasmânia | (UTC+10:00) Hobart |
| Hora padrão de Vladivostok | (UTC+10:00) Vladivostok |
| Fuso Horário da Ilha de Lord Howe | (UTC+10:30) Ilha de Lord Howe |
| Fuso Horário de Bougainville | (UTC+11:00) Ilha de Bougainville |
| Fuso horário de Rússia 10 | (UTC+11:00) Chokurdakh |
| Hora Padrão de Magadã | (UTC+11:00) Magadã |
| Fuso Horário de Norfolk | (UTC+11:00) Ilha de Norfolk |
| Fuso Horário de Sacalina | (UTC+11:00) Sacalina |
| Hora Padrão do Pacífico Central | (UTC+11:00) Ilhas Salomão, Nova Caledónia |
| Fuso horário da Rússia 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Hora Padrão da Nova Zelândia | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Hora Universal Coordenada +12 |
| Hora Padrão das Fiji | (UTC+12:00) Fiji |
| Hora Padrão de Kamchatka | (UTC+12:00) Petropavlovsk-Kamchatsky - Antigo |
| Fuso Horário das Ilhas Chatham | (UTC+12:45) Ilhas Chatham |
| UTC+13 | (UTC + 13:00) Hora Universal Coordenada+13 |
| Hora Padrão de Tonga | (UTC+13:00) Nuku'alofa |
| Hora Padrão de Samoa | (UTC+13:00) Samoa |
| Hora Padrão Ilhas Espórades Equatoriais | (UTC+14:00) Ilha de Kiritimati |

## <a name="see-also"></a>Consulte também 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
