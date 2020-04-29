---
title: Saiba mais sobre os mais recentes lançamentos do Os OS do Azure Guest / Microsoft Docs
description: As últimas notícias de lançamento e compatibilidade do SDK para o Os Convidado dos Serviços Azure Cloud.
services: cloud-services
documentationcenter: na
author: yohaddad
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 4/22/2020
ms.author: yohaddad
ms.openlocfilehash: 83ecbf05ab892e0673b31df893f66b03373957b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101506"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Lançamentos do Os do Azure Guest e matriz de compatibilidade sdk
Fornece-lhe informações atualizadas sobre os mais recentes lançamentos do Os OS do Azure Guest para serviços em nuvem. Esta informação ajuda-o a planear o seu caminho de upgrade antes de um Sistema operativo convidado ser desativado. Se configurar as suas funções para utilizar atualizações *automáticas* do Osdo do Hóspede, conforme descrito nas Definições de Atualização do Osso do [Hóspede do Azure,][Azure Guest OS Update Settings]não é vital que leia esta página.

> [!IMPORTANT]
> Esta página aplica-se às funções web e dos trabalhadores da Cloud Services, que funcionam em cima de um Os Convidado. Não **se aplica** às Máquinas Virtuais IaaS.
>
>


> [!TIP]
>  Subscreva o [Feed RSS] de Atualização do Os do Hóspede para receber a notificação mais oportuna em todas as alterações do Os do Hóspede.
>
>

> [!IMPORTANT]
> Apenas as mais recentes 2 versões do Guest OS serão suportadas e disponíveis no portal Azure.
>
>

Não tem a certeza de como atualizar o seu Sistema operativo convidado? Olha para [isto.][cloud updates]

## <a name="news-updates"></a>Atualizações de notícias

###### <a name="april-2-2020"></a>**2 de abril de 2020**
O Os convidado de março foi lançado. 

###### <a name="march-5-2020"></a>**5 de março de 2020**
O Os convidado de fevereiro foi lançado. 

###### <a name="january-24-2020"></a>**24 de janeiro de 2020**
O Os convidado de janeiro foi lançado. 

###### <a name="january-8-2020"></a>**8 de janeiro de 2020**
O Os convidado de dezembro foi lançado.

###### <a name="december-5-2019"></a>**5 de dezembro de 2019**
O Os convidado de novembro foi lançado.

###### <a name="november-1-2019"></a>**1 de novembro de 2019**
O Os convidado de outubro foi lançado.

###### <a name="october-7-2019"></a>**7 de outubro de 2019**
O Os convidado de setembro foi lançado.

###### <a name="september-4-2019"></a>**4 de setembro de 2019**
O Os convidado de agosto foi lançado.

###### <a name="july-26-2019"></a>**26 de julho de 2019**
O Os convidado de julho foi lançado.

###### <a name="july-8-2019"></a>**8 de julho de 2019**
O Os convidado de junho foi lançado.

###### <a name="june-6-2019"></a>**6 de junho de 2019**
O Os convidado de maio lançou.

###### <a name="may-7-2019"></a>**7 de maio de 2019**
O Os convidado de abril foi lançado.

###### <a name="march-26-2019"></a>**26 de março de 2019**
O Os convidado de março foi lançado.

###### <a name="march-12-2019"></a>**Março 12, 2019**
O Os convidado de fevereiro foi lançado.

###### <a name="february-5-2019"></a>**5 de fevereiro de 2019**
O Os convidado de janeiro foi lançado.

###### <a name="january-24-2019"></a>**24 de janeiro de 2019**
O Family 6 Guest OS (Windows Server 2019) foi lançado.

###### <a name="january-7-2019"></a>**7 de janeiro de 2019**
O Os convidado de dezembro foi lançado.

###### <a name="december-14-2018"></a>**14 de dezembro de 2018**
O Os convidado de novembro foi lançado.

###### <a name="november-8-2018"></a>**8 de novembro de 2018**
O Os convidado de outubro foi lançado.

###### <a name="october-12-2018"></a>**12 de outubro de 2018**
O Os convidado de setembro foi lançado.

## <a name="releases"></a>Versões

## <a name="family-6-releases"></a>Lançamentos da família 6
**Windows Server 2019**

.NET Quadro instalado: 3.5, 4.7.2

> [!NOTE]
> O Windows Azure SDK para .NET - 3.0 pode ser descarregado [aqui][Windows Azure SDK].
>
>Etapas de instalação:
>1. Desinstale quaisquer versões mais antigas do MicrosoftAzureAuthoringTools*.msi
>2. Instale o [Azure SDK para .NET - 3.0][Windows Azure SDK]
>3. Reinicie a sua máquina
>4. Crie um novo projeto de Serviço cloud e adicione um único papel de trabalhador
>5. Mude a Família OS para 6 e construa um pacote
>6. Implemente o pacote para Azure utilizando o portal Azure ou o Estúdio Visual
>
>O lançamento do Os Family 6 aplica o TLS 1.2 desativando explicitamente o TLS 1.0 e 1.1 e definindo um conjunto específico de suites de cifra. Saiba [mais.]


| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-6.17_202003-01  |  2 de abril de 2020  |  Posto 6.19  |  
|  WA-GUEST-OS-6.16_202002-01  |  5 de março de 2020  |  Posto 6.18  |  
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
|~~WA-GUEST-OS-6.4_201902-01~~ |Março 12, 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 de janeiro de 2019 |Março 12, 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 de janeiro de 2019 |5 de fevereiro de 2019 |

## <a name="family-5-releases"></a>Lançamentos da família 5
**Windows Server 2016**

.NET Quadro instalado: 3.5, 4.6.2

> [!NOTE]
> A palavra-passe RDP para a família OS 5 deve ser um mínimo de 10 caracteres.
>


| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-5.41_202003-01  |  2 de abril de 2020  |  Posto 5.43  |  
|  WA-GUEST-OS-5.40_202002-01  |  5 de março de 2020  |  Posto 5.42  |  
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
|~~WA-GUEST-OS-5.28_201902-01~~ |Março 12, 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 de janeiro de 2019 |Março 12, 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-4-releases"></a>Lançamentos da família 4
**Windows Server 2012 R2**

.NET Quadro instalado: 3.5, 4.5.1, 4.5.2

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-4.76_202003-01  |  2 de abril de 2020  |  Posto 4.78  |  
|  WA-GUEST-OS-4.75_202002-01  |  5 de março de 2020  |  Posto 4.77  |  
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
|~~WA-GUEST-OS-4.63_201902-01~~ |Março 12, 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 de janeiro de 2019 |Março 12, 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-3-releases"></a>Lançamentos da família 3
**Windows Server 2012**

.NET Quadro instalado: 3.5, 4.5

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-3.83_202003-01  |  2 de abril de 2020  |  Posto 3.85  |  
|  WA-GUEST-OS-3.82_202002-01  |  5 de março de 2020  |  Posto 3.84  |  
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
|~~WA-GUEST-OS-3.70_201902-01~~ |Março 12, 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 de janeiro de 2019 |Março 12, 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-2-releases"></a>Lançamentos da Família 2
**Windows Server 2008 R2 SP1**

.NET Quadro instalado: 3.5 (inclui 2.0 e 3.0), 4.5

| Cadeia de configuração | Data da versão | Data de desativação |
| --- | --- | --- |
|  WA-GUEST-OS-2.96_202003-01  |  2 de abril de 2020  |  Posto 2.98  |  
|  WA-GUEST-OS-2.95_202002-01  |  5 de março de 2020  |  Posto 2.97  |  
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
|~~WA-GUEST-OS-2.83_201902-01~~ |Março 12, 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 de janeiro de 2019 |Março 12, 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="msrc-patch-updates"></a>Atualizações de patch mSRC
A lista de patches que estão incluídos com cada versão mensal do Guest OS está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte de SKDs
Embora a política de [aposentadoria do SDK Azure][retire policy sdk] indique que apenas as versões acima de 2.2 são suportadas, as famílias específicas do Os OD permitem-lhe usar versões anteriores. Deve sempre utilizar o mais recente SDK suportado.

| Família de osso convidado | Versões SDK compatíveis |
| --- | --- |
| 6 |Versão 2.9.6+ |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1+ |
| 3 |Versão 1.8+ |
| 2 |Versão 1.3+ |
| 1 |Versão 1.0+ |

## <a name="guest-os-release-information"></a>Informações de lançamento do Os SO convidado
Existem três datas que são importantes para os lançamentos do Guest OS: data de **lançamento,** data **de desativação** e data de **validade.** Um Os convidado é considerado disponível quando está no Portal e pode ser selecionado como o os o si convidado alvo. Quando um Os Convidado atinge a data **desativada,** é removido do Azure. No entanto, qualquer Serviço cloud que direcione o Os do Hóspede continuará a funcionar normalmente.

A janela entre a data **dedesactivada** e a data de **validade** proporciona-lhe um tampão para facilmente transitar de um Os Convidado para um mais recente. Se estiver a usar *automaticamente* como o seu Sistema operativo convidado, estará sempre na versão mais recente e não tem de se preocupar com a sua caducidade.

Quando a data de **validade** passar, qualquer Serviço cloud ainda a utilizar esse Os do Hóspede será interrompido, eliminado ou forçado a fazer upgrade. Pode ler mais sobre a política de aposentadoria [aqui.][retirepolicy]

## <a name="guest-os-family-version-explanation"></a>Explicação da versão familiar do Os convidado
As famílias guest OS baseiam-se em versões lançadas do Microsoft Windows Server. O Os convidado é o sistema operativo subjacente ao funcionamento do Azure Cloud Services. Cada Os Convidado tem um número de família, versão e lançamento.

* **Família de osso convidado**  
  Um sistema operativo Windows Server liberta um Sistema Operativo De hóspedes baseado. Por exemplo, a *família 3* baseia-se no Windows Server 2012.
* **Versão do Os convidado**  
  Específico para uma imagem familiar do Guest OS e patches relevantes [do Microsoft Security Response Center (MSRC)][msrc] que estão disponíveis na data em que a nova versão do Os convidado é produzida. Nem todos os patches podem ser incluídos.

    Os números começam em 0 e incrementam por 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros de rasto só são mostrados se forem importantes. Ou seja, a versão 2.10 é uma versão diferente, muito mais tarde do que a versão 2.1.
* **Lançamento do Os convidado**  
  Um relançamento de uma versão guest OS. Ocorre uma reedição se a Microsoft encontrar problemas durante os testes; exigindo alterações. O último lançamento substitui sempre quaisquer lançamentos anteriores, públicos ou não. O portal Azure apenas permitirá que os utilizadores escolham o mais recente lançamento para uma determinada versão. As implementações em execução numa versão anterior não são geralmente atualizadas em função da gravidade do bug.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é o lançamento.

**Lançamento do Os** convidado - 2.12 rel2

**Cadeia de configuração para este lançamento** - WA-GUEST-OS-2.12_201208-02

A cadeia de configuração para um Os convidado tem as mesmas informações incorporadas no mesmo, juntamente com uma data que mostra quais os patches de MSRC considerados para esse lançamento. Neste exemplo, os patches MSRC produzidos para o Windows Server 2008 R2 até agosto de 2012 foram considerados para inclusão. Apenas estão incluídos patches que se aplicam especificamente a essa versão do Windows Server. Por exemplo, se um patch MSRC se aplicar ao Microsoft Office, este não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## <a name="guest-os-system-update-process"></a>Processo de atualização do sistema de sistema solista convidado
Esta página inclui informações sobre os próximos Lançamentos do OsS convidado. Os clientes indicaram que querem saber quando ocorre um lançamento porque as suas funções de serviço na nuvem serão reiniciadas se forem definidas para a atualização "Automática". Os lançamentos de Os os osonormalmente ocorrem 2-3 semanas após a versão da atualização do MSRC que ocorre na segunda terça-feira de cada mês. Os novos lançamentos incluem todos os patches mSRC relevantes para cada família de Os Convidados.

O Microsoft Azure está constantemente a lançar atualizações. O Os convidado é apenas uma dessas atualizações no pipeline. Uma libertação pode ser afetada por muitos fatores demasiado numerosos para listar aqui. Além disso, Azure funciona literalmente com centenas de milhares de máquinas. Isto significa que é impossível dar uma data e hora exatas quando o seu(s) papel(s) vai reiniciar. Estamos a trabalhar num plano para limitar ou reiniciar o tempo.

Quando um novo lançamento do Guest OS é publicado, pode levar tempo a propagar-se totalmente através do Azure. À medida que os serviços são atualizados para o novo Os convidado, são reiniciados em homenagem aos domínios de atualização. Os serviços definidos para utilizar atualizações "Automáticas" receberão primeiro um lançamento. Após a atualização, verá a nova versão Do Os do Hóspede listada para o seu serviço no portal Azure. Podem ocorrer relançamentos durante este período. Algumas versões podem ser implementadas durante períodos de tempo mais longos e as atualizações automáticas podem não ocorrer durante muitas semanas após a data de lançamento oficial. Uma vez que um Os convidado esteja disponível, pode então escolher explicitamente essa versão a partir do portal ou no seu ficheiro de configuração.

Para obter uma grande quantidade de informações valiosas sobre reinícios e indicações para mais informações técnicas de atualizações de Guest e Host OS, consulte a publicação de blog da MSDN intitulada [Role Instance Restarts Devido a Atualizações do OS][restarts].

Se atualizar manualmente o seu Os convidado, consulte a política de [aposentadoria do Os do Hóspede][retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de apoio ao OS e política de reforma
A política de apoio ao Os convidado e a política de aposentadoria é explicada [aqui.][retirepolicy]

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Feed RSS de atualização do OS convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[mais]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
