---
title: Saiba mais sobre as mais recentes versões do SO de convidado do Azure | Documentos da Microsoft
description: As últimas notícias de lançamento e a compatibilidade SDK para o SO convidado do Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/12/2019
ms.author: raiye
ms.openlocfilehash: c5fdee4e5b1b9d2a283ef55ce99dffab0cb25873
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840855"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Lançamentos de SO convidado do Azure e matriz de compatibilidade SDK
Fornece a que com informações atualizadas sobre o SO convidado do Azure mais recentes versões para serviços em nuvem. Estas informações ajudam a planear o caminho de atualização antes de SO convidado está desabilitado. Se configurar as funções para utilizar *automática* conforme descrito em de atualizações de SO convidado [definições de atualização de SO de convidado do Azure][Azure Guest OS Update Settings], não é vital que leia esta página.

> [!IMPORTANT]
> Esta página é aplicável a funções dos serviços Cloud web e de trabalho, que são executados sobre um SO convidado. Ele faz **não se aplicam** para máquinas de virtuais de IaaS.
>
>


> [!TIP]
>  Subscrever o [Feed RSS de atualização de SO convidado] para receber a notificação mais atempada relativamente a todas as alterações de SO convidado.
>
>

> [!IMPORTANT]
> Apenas as versões mais recentes de 2 do sistema operacional convidado será suportada e está disponível no portal do Azure.
>
>

Não sabe sobre como atualizar o SO convidado? Verifique [isso] [ cloud updates] horizontalmente.

## <a name="news-updates"></a>Notícias sobre atualizações

###### <a name="march-12-2019"></a>**12 de Março de 2019**
O sistema operacional convidado de Fevereiro já lançada.

###### <a name="february-5-2019"></a>**5 de Fevereiro de 2019**
O sistema operacional convidado de Janeiro já lançada.

###### <a name="january-24-2019"></a>**24 de Janeiro de 2019**
SO de convidado de família 6 (Windows Server 2019) já lançada.

###### <a name="january-7-2019"></a>**7 de Janeiro de 2019**
O sistema operacional convidado de Dezembro já lançada.

###### <a name="december-14-2018"></a>**14 de Dezembro de 2018**
Lançou o SO de convidado de Novembro.

###### <a name="november-8-2018"></a>**8 de Novembro de 2018**
Lançou o SO de convidado de Outubro.

###### <a name="october-12-2018"></a>**12 de Outubro de 2018**
Lançou o SO de convidado de Setembro.

###### <a name="september-12-2018"></a>**12 de Setembro de 2018**
Lançou o SO de convidado de Agosto.

## <a name="releases"></a>Versões

## <a name="family-6-releases"></a>Versões de família 6
**Windows Server 2019**

.NET framework instalado: 3.5, 4.7.2

> [!NOTE]
> O Windows Azure SDK para .NET - 3.0 pode ser baixado [aqui][Windows Azure SDK].
>
>Passos de instalação:
>1. Desinstale as versões mais antigas do MicrosoftAzureAuthoringTools*.msi
>2. Instalar o [Azure SDK para .NET - 3.0][Windows Azure SDK]
>3. Reiniciar a máquina
>4. Criar um novo projeto de serviço em nuvem e adicionar uma única função de trabalho
>5. Altere a família de SO para 6 e criar um pacote
>6. Implementar o pacote para o Azure com o portal do Azure ou o Visual Studio
>


| Cadeia de configuração | Data da versão | Desativar data |
| --- | --- | --- |
| WA-GUEST-OS-6.4_201902-01 |12 de Março de 2019 |Post 6.6 |
| WA-GUEST-OS-6.3_201901-01 |5 de Fevereiro de 2019 |Post 6.5 |
|~~WA-GUEST-OS-6.2_201812-01~~|24 de Janeiro de 2019 |12 de Março de 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 de Janeiro de 2019 |5 de Fevereiro de 2019 |

## <a name="family-5-releases"></a>Versões de família 5
**Windows Server 2016**

.NET framework instalado: 3.5, 4.6.2

> [!NOTE]
> A palavra-passe RDP para a família de SO 5 tem de ser um mínimo de 10 caracteres.
>


| Cadeia de configuração | Data da versão | Desativar data |
| --- | --- | --- |
| WA-GUEST-OS-5.28_201902-01 |12 de Março de 2019 |Post 5.30 |
| WA-GUEST-OS-5.27_201901-01 |5 de Fevereiro de 2019 |Post 5.29 |
|~~WA-GUEST-OS-5.26_201812-01~~|7 de Janeiro de 2019 |12 de Março de 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 de Dezembro de 2018 |5 de Fevereiro de 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 de novembro de 2018 |7 de Janeiro de 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 de Outubro de 2018 |14 de Dezembro de 2018 |
|~~WA-GUEST-OS-5.22_201808-01~~ |12 de Setembro de 2018 |8 de novembro de 2018 |

## <a name="family-4-releases"></a>Versões de família 4
**Windows Server 2012 R2**

.NET framework instalado: 3.5, 4.5.1

| Cadeia de configuração | Data da versão | Desativar data |
| --- | --- | --- |
| WA-GUEST-OS-4.63_201902-01 |12 de Março de 2019 |Post 4,65 |
| WA-GUEST-OS-4.62_201901-01 |5 de Fevereiro de 2019 |Post 4.64 |
|~~WA-GUEST-OS-4.61_201812-01~~|7 de Janeiro de 2019 |12 de Março de 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 de Dezembro de 2018 |5 de Fevereiro de 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 de novembro de 2018 |7 de Janeiro de 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 de Outubro de 2018 |14 de Dezembro de 2018 |
|~~WA-GUEST-OS-4.57_201808-01~~ |12 de Setembro de 2018 |8 de novembro de 2018 |

## <a name="family-3-releases"></a>Versões da família 3
**Windows Server 2012**

.NET framework instalado: 3.5, 4.5

| Cadeia de configuração | Data da versão | Desativar data |
| --- | --- | --- |
| WA-GUEST-OS-3.70_201902-01 |12 de Março de 2019 |Post 3.72 |
| WA-GUEST-OS-3.69_201901-01 |5 de Fevereiro de 2019 |Post 3.71 |
|~~WA-GUEST-OS-3.68_201812-01~~|7 de Janeiro de 2019 |12 de Março de 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 de Dezembro de 2018 |5 de Fevereiro de 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 de novembro de 2018 |7 de Janeiro de 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 de Outubro de 2018 |14 de Dezembro de 2018 |
|~~WA-GUEST-OS-3.64_201808-01~~ |12 de Setembro de 2018 |8 de novembro de 2018 |

## <a name="family-2-releases"></a>Versões de família 2
**Windows Server 2008 R2 SP1**

.NET framework instalado: 3.5 (inclui 2.0 e 3.0)

| Cadeia de configuração | Data da versão | Desativar data |
| --- | --- | --- |
| WA-GUEST-OS-2.83_201902-01 |12 de Março de 2019 |Post 2.85 |
| WA-GUEST-OS-2.82_201901-01 |5 de Fevereiro de 2019 |Post 2.84 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 de Janeiro de 2019 |12 de Março de 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 de Dezembro de 2018 |5 de Fevereiro de 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 de novembro de 2018 |7 de Janeiro de 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 de Outubro de 2018 |14 de Dezembro de 2018 |
|~~WA-GUEST-OS-2.77_201808-01~~ |12 de Setembro de 2018 |8 de novembro de 2018 |

## <a name="msrc-patch-updates"></a>Atualizações de patch do MSRC
A lista de patches que estão incluídos em cada versão de SO convidado mensal está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte do SDK
Apesar do [política de reforma para o Azure SDK] [ retire policy sdk] indica que apenas as versões acima 2.2 são suportados e específico famílias de SO convidado permitem-lhe utilizar versões anteriores. Deve sempre usar o SDK mais recente suportado.

| Família de SO convidado | Versões compatíveis do SDK |
| --- | --- |
| 6 |Versão 2.9.6+ |
| 5 |Versão 2.9.5.1+ |
| 4 |Versão 2.1 + |
| 3 |Versão 1.8 + |
| 2 |Versão 1.3 + |
| 1 |Versão 1.0 + |

## <a name="guest-os-release-information"></a>Informações de versão do SO convidado
Existem três datas que são importantes para versões de SO convidado: **libertar** data, **desativada** data, e **expiração** data. SO convidado é considerado disponível quando está no Portal e pode ser selecionado como o destino de SO convidado. Quando atingir o SO convidado do **desativada** data, este é removido do Azure. No entanto, qualquer serviço Cloud destinado nesse SO convidado continuará a funcionar normalmente.

A janela entre o **desativada** data e a **expiração** data fornece uma memória intermédia para transição facilmente de um SO de convidado para uma mais recente. Se estiver a utilizar *automática* como seu sistema operacional convidado, estar sempre a versão mais recente e não precisa se preocupar sobre ele prestes a expirar.

Quando o **expiração** data passa, qualquer serviço de nuvem ainda usando nesse SO convidado será parado, eliminado ou forçado a atualizar. Pode ler mais sobre a política de extinção [aqui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Explicação da versão da família de SO convidado
As famílias de SO convidado baseiam-se a versões atuais do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente que serviços Cloud do Azure é executado. Cada SO convidado tem uma família, a versão e a versão número.

* **Família de SO convidado**  
  Uma versão de sistema operativo Windows Server com base no SO convidado. Por exemplo, *família 3* baseia-se no Windows Server 2012.
* **Versão de SO convidado**  
  Específicos a uma imagem de família de SO convidado mais relevantes [Microsoft Security Response Center (MSRC)] [ msrc] patches que estão disponíveis na data a nova versão de SO convidado é produzida. Nem todos os patches possam estar incluídos.

    Números começam em 0 e incrementado em 1 sempre que é adicionado um novo conjunto de atualizações. Zeros à direita são exibidos apenas se importante. Ou seja, a versão 2.10 é uma versão diferente, muito posterior à versão 2.1.
* **Versão de SO convidado**  
  Um rerelease de uma versão de SO convidado. Um rerelease ocorre se o Microsoft encontrar problemas durante o teste; necessidade de alterações. Sempre a versão mais recente substitui qualquer anterior for lançada, público ou não. O portal do Azure só permitirá aos utilizadores escolher a versão mais recente para uma determinada versão. Implementações em execução numa versão anterior são geralmente não força atualizada dependendo da gravidade do bug.

No exemplo abaixo, 2 é a família, 12 é a versão e "rel2" é a versão.

**Lançamento do SO convidado** - 2.12 rel2

**Cadeia de configuração para esta versão** -WA-convidado-SO-2.12_201208-02

A cadeia de configuração para o SO convidado tem essas mesmas informações incorporadas, juntamente com uma data que mostra quais patches do MSRC foram consideradas para aquela versão. Neste exemplo, os patches do MSRC produzido para o Windows Server 2008 R2 até e incluindo Agosto de 2012 foram considerados para inclusão. Apenas os patches aplicar especificamente para essa versão do Windows Server são incluídos. Por exemplo, se um patch do MSRC se aplicar ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## <a name="guest-os-system-update-process"></a>Processo de atualização do sistema de SO convidado
Esta página inclui informações sobre futuras versões do SO convidado. Os clientes indicado que elas querem saber quando uma versão ocorre porque as respetivas funções de serviço em nuvem serão reiniciado se forem definidos para a atualização "Automática". Versões de SO convidado normalmente ocorrem 2 a 3 semanas depois do MSRC atualizar a versão que ocorre na segunda Terça-feira de cada mês. Novas versões incluem os patches MSRC relevantes para cada família de SO convidado.

Microsoft Azure está constantemente a lançar atualizações. O SO convidado é apenas um desse atualização no pipeline. Uma versão pode ser afetada por diversos fatores numerosos para relacionar aqui. Além disso, o Azure é executado numa literalmente centenas de milhares de máquinas. Isso significa que é impossível dar uma data e hora exatas quando as suas funções serão reiniciado. Estamos a trabalhar num plano para limitar ou reinícios de tempo.

Quando uma nova versão do SO convidado é publicada, pode demorar tempo para propagar totalmente no Azure. À medida que os serviços são atualizados para o novo SO convidado, são reiniciados a respeitar os domínios de atualização. Serviços para utilizar atualizações de "Automático" irão obter uma versão primeiro. Após a atualização, verá a nova versão de SO convidado listada para o seu serviço no portal do Azure. Rereleases pode ocorrer durante esse período. Algumas versões podem ser implementadas ao longo de períodos de tempo mais longos e reinicializações de atualização automática não podem ocorrer durante várias semanas após a data de lançamento oficial. Assim que o SO convidado está disponível, pode explicitamente escolher essa versão do portal ou no ficheiro de configuração.

Para uma grande quantidade de informações importantes sobre reinicializações e ponteiros para obter mais detalhes técnicos de informações de atualizações de convidado e o SO anfitrião, consulte o MSDN postagem no blog intitulada [função de instância é reiniciado devido a atualizações do SO] [ restarts].

Caso atualize manualmente o SO convidado, consulte a [política de extinção de SO convidado] [ retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de suporte e extinção de SO convidado
A política de suporte e extinção de SO convidado é explicada [aqui][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Feed RSS de atualização de SO convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
