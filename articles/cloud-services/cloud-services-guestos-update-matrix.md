---
title: Conheça os mais recentes lançamentos do Azure Guest OS | Microsoft Docs
description: As últimas notícias de lançamento e compatibilidade da SDK para o Azure Cloud Services Guest OS.
services: cloud-services
documentationcenter: na
author: gunnarcms
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 2/19/2021
ms.author: gunnarc
ms.openlocfilehash: f60df0c18368af46c5f2946b5668ac404c1fae90
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124555"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Lançamentos do Azure Guest OS e matriz de compatibilidade SDK
Fornece-lhe informações atualizadas sobre as mais recentes versões do Azure Guest OS para serviços cloud. Estas informações ajudam-no a planear o seu caminho de upgrade antes de um So convidado ser desativado. Se configurar as suas funções para utilizar atualizações *automáticas* do Guest OS, conforme descrito nas [Definições de Atualização do Azure Guest OS,][Azure Guest OS Update Settings]não é vital que leia esta página.

> [!IMPORTANT]
> Esta página aplica-se aos papéis web e trabalhadores da Cloud Services, que funcionam em cima de um Sistema operativo convidado. Não **se aplica às** Máquinas Virtuais IaaS.
>
>


> [!TIP]
>  Subscreva o [Feed RSS RSS da Atualização de Hóspedes] para receber a notificação mais atempadamente em todas as alterações do Guest OS.
>
>

> [!IMPORTANT]
> Apenas as mais recentes 2 versões do So convidado serão suportadas e disponíveis no portal Azure.
>
>

Não tem a certeza de como atualizar o seu SO convidado? Olha para [isto.][cloud updates]

## <a name="news-updates"></a>Atualizações de notícias

###### <a name="february-19-2021"></a>**19 de fevereiro de 2021**
O Os Convidados de fevereiro foi lançado. 

###### <a name="february-5-2021"></a>**5 de fevereiro de 2021**
O So convidado de janeiro foi lançado. 

###### <a name="january-15-2021"></a>**15 de janeiro de 2021**
O Os Convidados de dezembro foi lançado. 

###### <a name="december-19-2020"></a>**19 de dezembro de 2020**
O Convidado OS de novembro foi lançado. 

###### <a name="november-17-2020"></a>**17 de novembro de 2020**
O Os Convidados de outubro foi lançado. 

###### <a name="october-10-2020"></a>**10 de outubro de 2020**
O Convidado de setembro foi lançado. 

###### <a name="september-5-2020"></a>**5 de setembro de 2020**
O So convidado de agosto foi lançado. 

###### <a name="august-17-2020"></a>**17 de agosto de 2020**
O Os Convidados de julho foi lançado. 

###### <a name="august-10-2020"></a>**10 de agosto de 2020**
O June Guest OS foi lançado. 

###### <a name="june-2-2020"></a>**2 de junho de 2020**
O So convidado de maio foi lançado. 

###### <a name="may-4-2020"></a>**4 de maio de 2020**
O Os Convidados de abril foi lançado. 

###### <a name="april-2-2020"></a>**2 de abril de 2020**
O Os Convidados de março foi lançado. 

###### <a name="march-5-2020"></a>**5 de março de 2020**
O Os Convidados de fevereiro foi lançado. 

###### <a name="january-24-2020"></a>**24 de janeiro de 2020**
O So convidado de janeiro foi lançado. 

###### <a name="january-8-2020"></a>**8 de janeiro de 2020**
O Os Convidados de dezembro foi lançado.

###### <a name="december-5-2019"></a>**5 de dezembro de 2019**
O Convidado OS de novembro foi lançado.

###### <a name="november-1-2019"></a>**1 de novembro de 2019**
O Os Convidados de outubro foi lançado.

###### <a name="october-7-2019"></a>**7 de outubro de 2019**
O Convidado de setembro foi lançado.

###### <a name="september-4-2019"></a>**4 de setembro de 2019**
O So convidado de agosto foi lançado.

###### <a name="july-26-2019"></a>**26 de julho de 2019**
O Os Convidados de julho foi lançado.

###### <a name="july-8-2019"></a>**8 de julho de 2019**
O June Guest OS foi lançado.

###### <a name="june-6-2019"></a>**6 de junho de 2019**
O So convidado de maio foi lançado.

###### <a name="may-7-2019"></a>**7 de maio de 2019**
O Os Convidados de abril foi lançado.

###### <a name="march-26-2019"></a>**26 de março de 2019**
O Os Convidados de março foi lançado.

###### <a name="march-12-2019"></a>**12 de março de 2019**
O Os Convidados de fevereiro foi lançado.

###### <a name="february-5-2019"></a>**5 de fevereiro de 2019**
O So convidado de janeiro foi lançado.

###### <a name="january-24-2019"></a>**24 de janeiro de 2019**
Family 6 Guest OS (Windows Server 2019) foi lançado.

###### <a name="january-7-2019"></a>**7 de janeiro de 2019**
O Os Convidados de dezembro foi lançado.

###### <a name="december-14-2018"></a>**14 de dezembro de 2018**
O Convidado OS de novembro foi lançado.

###### <a name="november-8-2018"></a>**8 de novembro de 2018**
O Os Convidados de outubro foi lançado.

###### <a name="october-12-2018"></a>**12 de outubro de 2018**
O Convidado de setembro foi lançado.

## <a name="releases"></a>Versões

## <a name="family-6-releases"></a>Libertações da família 6
**Windows Server 2019**

.NET Quadro instalado: 3.5, 4.7.2

> [!NOTE]
> O Windows Azure SDK para .NET - 3.0 pode ser descarregado [aqui][Windows Azure SDK].
>
>Etapas de instalação:
>1. Desinstale quaisquer versões mais antigas do MicrosoftAzureAuthoringTools*.msi
>2. Instale o [Azure SDK para .NET - 3.0][Windows Azure SDK]
>3. Reinicie a sua máquina
>4. Crie um novo projeto de Cloud Service e adicione um único papel de trabalhador
>5. Mude a Família OS para 6 e construa um pacote
>6. Implemente o pacote para a Azure utilizando o portal Azure ou o Visual Studio
>
>O lançamento do Guest OS Family 6 impõe o TLS 1.2 desativando explicitamente os TLS 1.0 e 1.1 e definindo um conjunto específico de suites de cifra. Saiba [mais.]


| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-6.28_202102-01 |  19 de fevereiro de 2021  |  Posto 6.30  |  
|  WA-GUEST-OS-6.27_202101-01 |  5 de fevereiro de 2021  |  Posto 6.29  |  
|~~WA-GUEST-OS-6.26_202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-6.25_202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-6.24_202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-6.23_202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-6.22_202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-6.21_202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-6.20_202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-6.19_202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-6.18_202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-6.17_202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-6.16_202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-6.15_202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-6.13_201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-6.12_201910-01~~| 1 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-6.11_201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-6.10_201908-01~~| 4 de agosto de 2019 | 1 de novembro de 2019  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-6.8_201906-01~~|8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-6.7_201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 de janeiro de 2019 |5 de fevereiro de 2019 |

## <a name="family-5-releases"></a>Libertações da família 5
**Windows Server 2016**

.NET Quadro instalado: 3.5, 4.6.2

> [!NOTE]
> A palavra-passe RDP para a família OS 5 deve ser um mínimo de 10 caracteres.
>


| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-5.52_202102-01  |  19 de fevereiro de 2021  |  Posto 5.54  | 
|  WA-GUEST-OS-5.51_202101-01  |  5 de fevereiro de 2021  |  Posto 5.53  | 
|~~WA-GUEST-OS-5.50_202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  | 
|~~WA-GUEST-OS-5.49_202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  | 
|~~WA-GUEST-OS-5.48_202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  | 
|~~WA-GUEST-OS-5.47_202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  | 
|~~WA-GUEST-OS-5.46_202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-5.45_202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-5.44_202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-5.43_202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-5.42_202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-5.41_202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-5.40_202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-5.39_202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-5.35_201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  4 de agosto de 2019  | 1 de novembro de 2019 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |  
|~~WA-GUEST-OS-5.32_201906-01~~|8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-4-releases"></a>Libertações da família 4
**Windows Server 2012 R2**

.NET Quadro instalado: 3.5, 4.5.1, 4.5.2

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-4.87_202102-01  |  19 de fevereiro de 2021  |  Posto 4.89  | 
|  WA-GUEST-OS-4.86_202101-01  |  5 de fevereiro de 2021  |  Posto 4.88  | 
|~~WA-GUEST-OS-4.85_202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  | 
|~~WA-GUEST-OS-4.84_202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  | 
|~~WA-GUEST-OS-4.83_202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  | 
|~~WA-GUEST-OS-4.82_202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  | 
|~~WA-GUEST-OS-4.81_202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  | 
|~~WA-GUEST-OS-4.80_202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  | 
|~~WA-GUEST-OS-4.79_202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  | 
|~~WA-GUEST-OS-4.78_202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-4.77_202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-4.76_202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-4.75_202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-4.74_202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-4.72_201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-4.71_201910-01~~| 1 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-4.70_201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-4.69_201908-01~~| 4 de agosto de 2019 | 1 de novembro de 2019 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26 de julho de 2019  | 7 de outubro de 2019 |
|~~WA-GUEST-OS-4.67_201906-01~~| 8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-3-releases"></a>Libertações da família 3
**Windows Server 2012**

.NET Quadro instalado: 3.5, 4.5

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-3.94_202102-01  |  19 de fevereiro de 2021  |  Posto 3.96  |
|  WA-GUEST-OS-3.93_202101-01  |  5 de fevereiro de 2021  |  Posto 3.95  |
|~~WA-GUEST-OS-3.92_202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-3.91_202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-3.90_202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-3.89_202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-3.88_202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020  |  
|~~WA-GUEST-OS-3.87_202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-3.86_202006-02~~|  10 de agosto de 2020  |  5 de setembro de 2020  |  
|~~WA-GUEST-OS-3.85_202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-3.84_202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-3.83_202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-3.82_202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-3.81_202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-3.77_201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  4 de agosto de 2019  |  1 de novembro de 2019  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| 8 de julho de 2019 |4 de agosto de 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-2-releases"></a>Libertações da Família 2
**Windows Server 2008 R2 SP1**

.NET Quadro instalado: 3.5 (inclui 2.0 e 3.0), 4.5

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-2.107_202102-01  |  19 de fevereiro de 2021  |  Posto 2.109  |  
|  WA-GUEST-OS-2.106_202101-01  |  5 de fevereiro de 2021  |  Posto 2.108  |  
|~~WA-GUEST-OS-2.105_202012-01~~|  15 de janeiro de 2021  |  19 de fevereiro de 2021  |  
|~~WA-GUEST-OS-2.104_202011-01~~|  19 de dezembro de 2020  |  5 de fevereiro de 2021  |  
|~~WA-GUEST-OS-2.103_202010-02~~|  17 de novembro de 2020  |  15 de janeiro de 2021  |  
|~~WA-GUEST-OS-2.102_202009-01~~|  10 de outubro de 2020  |  19 de dezembro de 2020  |  
|~~WA-GUEST-OS-2.101_202008-02~~|  5 de setembro de 2020  |  17 de novembro de 2020 |    
|~~WA-GUEST-OS-2.100_202007-01~~|  17 de agosto de 2020  |  10 de outubro de 2020  |  
|~~WA-GUEST-OS-2.99_202006-02~~|  10 de agosto de 2020  | 5 de setembro de 2020  |  
|~~WA-GUEST-OS-2.98_202005-02~~|  2 de junho de 2020  |  17 de agosto de 2020  |  
|~~WA-GUEST-OS-2.97_202004-01~~|  4 de maio de 2020  |  10 de agosto de 2020  |  
|~~WA-GUEST-OS-2.96_202003-01~~|  2 de abril de 2020  |  2 de junho de 2020  |  
|~~WA-GUEST-OS-2.95_202002-01~~|  5 de março de 2020  |  4 de maio de 2020  |  
|~~WA-GUEST-OS-2.94_202001-01~~|  24 de janeiro de 2020  |  2 de abril de 2020  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 8 de janeiro de 2020 | 5 de março de 2020 |  
|~~WA-GUEST-OS-2.92_201911-01~~| 5 de dezembro de 2019 | 24 de janeiro de 2020 |  
|~~WA-GUEST-OS-2.91_201910-01~~| 1 de novembro de 2019 | 8 de janeiro de 2020 |  
|~~WA-GUEST-OS-2.90_201909-01~~| 7 de outubro de 2019 | 5 de dezembro de 2019 |  
|~~WA-GUEST-OS-2.89_201908-01~~| 4 de agosto de 2019 | 1 de novembro de 2019 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26 de julho de 2019 | 7 de outubro de 2019 |
|~~WA-GUEST-OS-2.87_201906-01~~|8 de julho de 2019 | 4 de agosto de 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |6 de junho de 2019 |26 de julho de 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="msrc-patch-updates"></a>Atualizações de patch msrc
A lista de patches que estão incluídos com cada lançamento mensal do Guest OS está disponível [aqui.][patches]

## <a name="sdk-support"></a>Suporte de SKDs
Embora a [política de aposentadoria do Azure SDK][retire policy sdk] indique que apenas as versões acima de 2.2 são suportadas, famílias específicas de Guest OS permitem-lhe usar versões anteriores. Deve sempre utilizar os últimos SDK suportados.

| Família de SO convidado | Versões SDK compatíveis |
| --- | --- |
| 6 |Versão 2.9.6+ |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1+ |
| 3 |Versão 1.8+ |
| 2 |Versão 1.3+ |
| 1 |Versão 1.0+ |

## <a name="guest-os-release-information"></a>Informações de lançamento do OS convidados
Existem três datas que são importantes para as versões do Guest OS: data **de lançamento,** data **de desativada** e data **de validade.** Um So convidado é considerado disponível quando está no Portal e pode ser selecionado como o so-alvo do Guest. Quando um SO convidado atinge a data **de desativada,** é removido do Azure. No entanto, qualquer Serviço cloud que tenha como alvo o so do Hóspede continuará a funcionar normalmente.

A janela entre a data **de desativada** e a data **de validade** proporciona-lhe um tampão para facilmente transitar de um So convidado para um mais recente. Se estiver a utilizar *o sistema automático* como o seu SoO convidado, estará sempre na versão mais recente e não terá de se preocupar com a sua expiração.

Quando a data **de validade** passar, qualquer Serviço cloud ainda usando que o So convidado será parado, eliminado ou forçado a fazer upgrade. Pode ler mais sobre a política de aposentadoria [aqui.][retirepolicy]

## <a name="guest-os-family-version-explanation"></a>Explicação da versão familiar do Guest OS
As famílias de OS convidados baseiam-se em versões lançadas do Microsoft Windows Server. O Guest OS é o sistema operativo subjacente que a Azure Cloud Services funciona. Cada os hóspedes têm um número de família, versão e lançamento.

* **Família de SO convidado**  
  Um sistema operativo windows server desbloqueia que um sistema de serviço de hóspedes se baseia. Por exemplo, *a família 3* é baseada no Windows Server 2012.
* **Versão de SO convidado**  
  Específico para uma imagem familiar do Guest OS e patches relevantes [do Microsoft Security Response Center (MSRC)][msrc] que estão disponíveis na data em que a nova versão guest OS é produzida. Nem todos os patches podem ser incluídos.

    Os números começam em 0 e incrementam por 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros de fuga só são mostrados se for importante. Ou seja, a versão 2.10 é uma versão diferente, muito mais tarde do que a versão 2.1.
* **Lançamento do OS convidado**  
  Um relanço de uma versão guest OS. Um relanço ocorre se a Microsoft encontrar problemas durante os testes; exigindo alterações. O mais recente lançamento substitui sempre quaisquer lançamentos anteriores, públicos ou não. O portal Azure apenas irá permitir que os utilizadores escolham as mais recentes versões para uma determinada versão. As implementações em execução numa versão anterior não são normalmente atualizadas em função da gravidade do erro.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Lançamento do Guest OS** - 2.12 rel2

**Cadeia de configuração para este lançamento** - WA-GUEST-OS-2.12_201208-02

A cadeia de configuração de um Sistema operativo convidado tem esta mesma informação incorporada no mesmo, juntamente com uma data que mostra quais patches DE MSRC foram considerados para essa versão. Neste exemplo, foram considerados para inclusão patches MSRC produzidos para o Windows Server 2008 R2 até agosto de 2012. Apenas estão incluídos patches especificamente aplicáveis a essa versão do Windows Server. Por exemplo, se um patch DE MSRC se aplicar ao Microsoft Office, este não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## <a name="guest-os-system-update-process"></a>Processo de atualização do sistema de SISTEMA de Hóspedes
Esta página inclui informações sobre os próximos lançamentos do Guest OS. Os clientes indicaram que querem saber quando ocorre um lançamento porque as suas funções de serviço na nuvem serão reiniciadas se estiverem definidas para a atualização "Automática". Os lançamentos do Guest OS ocorrem normalmente 2-3 semanas após o lançamento da atualização MSRC que ocorre na segunda terça-feira de cada mês. Os novos lançamentos incluem todos os patches MSRC relevantes para cada família guest OS.

O Microsoft Azure está constantemente a lançar atualizações. O Guest OS é apenas uma dessas atualizações no pipeline. Uma libertação pode ser afetada por muitos fatores demasiado numerosos para listar aqui. Além disso, Azure corre em literalmente centenas de milhares de máquinas. Isto significa que é impossível dar uma data e hora exatas quando o seu(s) papel(s) será reiniciado. Estamos a trabalhar num plano para limitar ou reiniciar o tempo.

Quando um novo lançamento do So convidado é publicado, pode levar tempo para se propagar totalmente através do Azure. À medida que os serviços são atualizados para o novo Sistema de Hóspedes, são reiniciados honrando os domínios de atualização. Os serviços definidos para utilizar atualizações "Automáticas" terão um lançamento primeiro. Após a atualização, verá a nova versão guest OS listada para o seu serviço no portal Azure. Podem ocorrer relanços durante este período. Algumas versões podem ser implementadas durante períodos de tempo mais longos e podem não ocorrer reboots de atualização automática durante muitas semanas após a data oficial de lançamento. Uma vez que um So convidado esteja disponível, pode então escolher explicitamente essa versão a partir do portal ou no seu ficheiro de configuração.

Para obter uma grande quantidade de informações valiosas sobre reinícios e ponteiros para mais detalhes técnicos de informações sobre atualizações do Guest and Host OS, consulte o post de blog da MSDN intitulado [Role Instance Restarts Devido a Atualizações de OS][restarts].

Se atualizar manualmente o seu So convidado, consulte a [política de aposentadoria do Guest OS][retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de apoio ao OS e à política de aposentadoria
A política de apoio e aposentadoria do Guest OS é explicada [aqui.][retirepolicy]

[cloud updates]: ./cloud-services-update-azure-service.md
[Feed RSS da atualização RSS do convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: ./cloud-services-dotnet-install-dotnet.md?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: /security-updates/SecurityAdvisories/2015/3009008
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: /security-updates/SecurityBulletins/2014/ms14-066
[MS14-046]: /security-updates/SecurityBulletins/2014/ms14-046
[retire policy sdk]: /previous-versions/azure/reference/dn479282(v=azure.100)
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: /archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: /security-updates/SecurityBulletins/2017/ms17-010
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[mais]: ./applications-dont-support-tls-1-2.md
