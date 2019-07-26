---
title: Saiba mais sobre as versões mais recentes do sistema operacional convidado do Azure | Microsoft Docs
description: As notícias de versão mais recentes e a compatibilidade do SDK para o SO convidado dos serviços de nuvem do Azure.
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
ms.date: 7/18/2019
ms.author: raiye
ms.openlocfilehash: 2dac76ef0d0d71c11291e63fdb3a7ce307638b50
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405554"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Versões do SO convidado do Azure e matriz de compatibilidade do SDK
Fornece informações atualizadas sobre as versões mais recentes do sistema operacional convidado do Azure para serviços de nuvem. Essas informações ajudam a planejar o caminho de atualização antes que um sistema operacional convidado seja desabilitado. Se você configurar suas funções para usar  atualizações automáticas do sistema operacional convidado, conforme descrito em [configurações de atualização do SO convidado do Azure][Azure Guest OS Update Settings], não é vital que você leia esta página.

> [!IMPORTANT]
> Esta página se aplica a funções Web e de trabalho de serviços de nuvem, que são executadas sobre um sistema operacional convidado. Ele **não se aplica** a máquinas virtuais IaaS.
>
>


> [!TIP]
>  Assine o [RSS feed de atualização do SO convidado] para receber a notificação mais oportuna sobre todas as alterações do sistema operacional convidado.
>
>

> [!IMPORTANT]
> Somente as duas versões mais recentes do SO convidado terão suporte e estarão disponíveis no portal do Azure.
>
>

Não tem certeza de como atualizar seu sistema operacional convidado? Verifique [isso][cloud updates] .

## <a name="news-updates"></a>Atualizações de notícias

###### <a name="july-8-2019"></a>**8 de julho de 2019**
O SO convidado de junho foi lançado.

###### <a name="june-6-2019"></a>**6 de junho de 2019**
O sistema operacional convidado de maio foi lançado.

###### <a name="may-7-2019"></a>**7 de maio de 2019**
O SO convidado de abril foi lançado.

###### <a name="march-26-2019"></a>**26 de março de 2019**
O SO convidado de março foi lançado.

###### <a name="march-12-2019"></a>**12 de março de 2019**
O SO convidado de fevereiro foi lançado.

###### <a name="february-5-2019"></a>**5 de fevereiro de 2019**
O SO convidado de Janeiro foi lançado.

###### <a name="january-24-2019"></a>**24 de janeiro de 2019**
O sistema operacional convidado da família 6 (Windows Server 2019) foi lançado.

###### <a name="january-7-2019"></a>**7 de janeiro de 2019**
O SO convidado de dezembro foi lançado.

###### <a name="december-14-2018"></a>**14 de dezembro de 2018**
O sistema operacional convidado de novembro foi lançado.

###### <a name="november-8-2018"></a>**8 de novembro de 2018**
O sistema operacional convidado de outubro foi lançado.

###### <a name="october-12-2018"></a>**12 de outubro de 2018**
O sistema operacional convidado de setembro foi lançado.

## <a name="releases"></a>Versões

## <a name="family-6-releases"></a>Versões da família 6
**Windows Server 2019**

.NET Framework instalado: 3.5, 4.7.2

> [!NOTE]
> O SDK do Windows Azure para .NET-3,0 pode ser baixado [aqui][Windows Azure SDK].
>
>Etapas de instalação:
>1. Desinstale todas as versões mais antigas do MicrosoftAzureAuthoringTools*. msi
>2. Instalar o [SDK do Azure para .net-3,0][Windows Azure SDK]
>3. Reiniciar o computador
>4. Criar um novo projeto de serviço de nuvem e adicionar uma única função de trabalho
>5. Altere a família do sistema operacional para 6 e crie um pacote
>6. Implantar o pacote no Azure usando o portal do Azure ou o Visual Studio
>


| Cadeia de caracteres de configuração | Data de lançamento | Data de desabilitação |
| --- | --- | --- |
| WA-GUEST-OS-6.8_201906-01 |8 de julho de 2019 |Post 6,10 |
| WA-GUEST-OS-6.7_201905-01 |6 de junho de 2019 |Post 6,9 |
|~~WA-GUEST-OS-6.6_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 de janeiro de 2019 |5 de fevereiro de 2019 |

## <a name="family-5-releases"></a>Versões da família 5
**Windows Server 2016**

.NET Framework instalado: 3,5, 4,6

> [!NOTE]
> A senha RDP para a família de sistemas operacionais 5 deve ter no mínimo 10 caracteres.
>


| Cadeia de caracteres de configuração | Data de lançamento | Data de desabilitação |
| --- | --- | --- |
| WA-GUEST-OS-5.32_201906-01 |8 de julho de 2019 |Post 5,34 |
| WA-GUEST-OS-5.31_201905-01 |6 de junho de 2019 |Post 5,33 |
|~~WA-GUEST-OS-5.30_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-4-releases"></a>Versões da família 4
**Windows Server 2012 R2**

.NET Framework instalado: 3.5, 4.5.1, 4.5.2

| Cadeia de caracteres de configuração | Data de lançamento | Data de desabilitação |
| --- | --- | --- |
| WA-GUEST-OS-4.67_201906-01 |8 de julho de 2019 |Post 4,69 |
| WA-GUEST-OS-4.66_201905-01 |6 de junho de 2019 |Post 4,68 |
|~~WA-GUEST-OS-4.65_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-3-releases"></a>Versões da família 3
**Windows Server 2012**

.NET Framework instalado: 3,5, 4,5

| Cadeia de caracteres de configuração | Data de lançamento | Data de desabilitação |
| --- | --- | --- |
| WA-GUEST-OS-3.74_201906-01 |8 de julho de 2019 |Post 3,76 |
| WA-GUEST-OS-3.73_201905-01 |6 de junho de 2019 |Post 3,75 |
|~~WA-GUEST-OS-3.72_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="family-2-releases"></a>Versões da família 2
**Windows Server 2008 R2 SP1**

.NET Framework instalado: 3,5 (inclui 2,0 e 3,0), 4,5

| Cadeia de caracteres de configuração | Data de lançamento | Data de desabilitação |
| --- | --- | --- |
| WA-GUEST-OS-2.87_201906-01 |8 de julho de 2019 |Post 2,89 |
| WA-GUEST-OS-2.86_201905-01 |6 de junho de 2019 |Post 2,88 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7 de maio de 2019 |8 de julho de 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 de março de 2019 |6 de junho de 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 de março de 2019 |7 de maio de 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 de fevereiro de 2019 |26 de março de 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 de janeiro de 2019 |12 de março de 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 de dezembro de 2018 |5 de fevereiro de 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 de novembro de 2018 |7 de janeiro de 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 de outubro de 2018 |14 de dezembro de 2018 |

## <a name="msrc-patch-updates"></a>Atualizações de patch do MSRC
A lista de patches que estão incluídos em cada versão mensal do sistema operacional convidado está disponível [aqui][patches].

## <a name="sdk-support"></a>Suporte a SDK
Embora a política de desativação do [SDK do Azure][retire policy sdk] indique que apenas as versões acima de 2,2 têm suporte, as famílias de sistemas operacionais convidados específicos permitem que você use versões anteriores. Você sempre deve usar o SDK com suporte mais recente.

| Família do SO convidado | Versões compatíveis do SDK |
| --- | --- |
| 6 |Versão 2.9.6 + |
| 5 |Versão 2.9.5.1 + |
| 4 |Versão 2.1 + |
| 3 |Versão 1.8 + |
| 2 |Versão 1.3 + |
| 1 |Versão 1.0 + |

## <a name="guest-os-release-information"></a>Informações de versão do SO convidado
Há três datas que são importantes para as versões do sistema operacional convidado: data de **lançamento** , data de **desabilitação** e data de **validade** . Um SO convidado é considerado disponível quando está no portal e pode ser selecionado como o SO convidado de destino. Quando um sistema operacional convidado atinge  a data de desabilitação, ele é removido do Azure. No entanto, qualquer serviço de nuvem destinado ao SO convidado ainda funcionará normalmente.

A janela entre a  data de desabilitação e a data de **validade** fornece um buffer para fazer a transição facilmente de um sistema operacional convidado para um mais recente. Se você estiver usando *automático* como seu sistema operacional convidado, você estará sempre na versão mais recente e não precisará se preocupar com ele expirar.

Quando a  data de expiração for aprovada, qualquer serviço de nuvem que ainda estiver usando o SO convidado será interrompido, excluído ou forçado a ser atualizado. Você pode ler mais sobre a política de desativação [aqui][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Família do SO convidado-explicação da versão
As famílias do sistema operacional convidado baseiam-se nas versões lançadas do Microsoft Windows Server. O SO convidado é o sistema operacional subjacente no qual os serviços de nuvem do Azure são executados. Cada sistema operacional convidado tem uma família, versão e número de versão.

* **Família do SO convidado**  
  Uma versão do sistema operacional Windows Server na qual um SO convidado se baseia. Por exemplo, a *família 3* é baseada no Windows Server 2012.
* **Versão do SO convidado**  
  Específico de uma imagem da família de sistemas operacionais convidados, além dos patches relevantes do [MSRC (Microsoft Security Response Center)][msrc] disponíveis na data em que a nova versão do sistema operacional convidado é produzida. Nem todos os patches podem ser incluídos.

    Os números começam em 0 e são incrementados em 1 cada vez que um novo conjunto de atualizações é adicionado. Os zeros à direita são mostrados apenas se forem importantes. Ou seja, a versão 2,10 é uma versão diferente, muito posterior à versão 2,1.
* **Versão do SO convidado**  
  Um relançamento de uma versão do SO convidado. Uma reversão ocorrerá se A Microsoft encontrar problemas durante o teste; exigindo alterações. A versão mais recente sempre substitui todas as versões anteriores, públicas ou não. O portal do Azure só permitirá que os usuários escolham a versão mais recente para uma determinada versão. As implantações em execução em uma versão anterior geralmente não são atualizadas de força, dependendo da severidade do bug.

No exemplo a seguir, 2 é a família, 12 é a versão e "REL2" é a versão.

**Versão do SO convidado** -2,12 REL2

**Cadeia de caracteres de configuração para esta versão** -wa-Guest-os-2.12 _201208-02

A cadeia de caracteres de configuração para um SO convidado tem essas mesmas informações inseridas nela, juntamente com uma data mostrando quais patches do MSRC foram considerados para essa versão. Neste exemplo, os patches do MSRC produzidos para o Windows Server 2008 R2 até e incluindo agosto de 2012 foram considerados para inclusão. Somente os patches que se aplicam especificamente a essa versão do Windows Server são incluídos. Por exemplo, se um patch do MSRC se aplicar ao Microsoft Office, ele não será incluído porque esse produto não faz parte da imagem base do Windows Server.

## <a name="guest-os-system-update-process"></a>Processo de atualização do sistema do SO convidado
Esta página inclui informações sobre as próximas versões do sistema operacional convidado. Os clientes indicaram que desejam saber quando uma versão ocorre porque suas funções de serviço de nuvem serão reinicializadas se estiverem definidas como atualização "automática". As versões do sistema operacional convidado normalmente ocorrem 2-3 semanas após a versão de atualização do MSRC que ocorre na segunda terça-feira de cada mês. As novas versões incluem todos os patches relevantes do MSRC para cada família de sistemas operacionais convidados.

Microsoft Azure está constantemente lançando atualizações. O SO convidado é apenas uma dessas atualizações no pipeline. Uma versão pode ser afetada por muitos fatores que são muito numerosos para listar aqui. Além disso, o Azure é executado em literalmente centenas de milhares de computadores. Isso significa que é impossível fornecer uma data e hora exatas em que a (s) função (ões) será reiniciada. Estamos trabalhando em um plano para limitar ou cronometrar as reinicializações.

Quando uma nova versão do sistema operacional convidado é publicada, pode levar tempo para se propagar totalmente pelo Azure. À medida que os serviços são atualizados para o novo sistema operacional convidado, eles são reinicializados respeitando OS domínios de atualização. Os serviços definidos para usar atualizações "automáticas" receberão uma versão primeiro. Após a atualização, você verá a nova versão do sistema operacional convidado listada para seu serviço no portal do Azure. As reversões podem ocorrer durante esse período. Algumas versões podem ser implantadas por longos períodos de tempo e as reinicializações de atualização automática podem não ocorrer por muitas semanas após a data de lançamento oficial. Depois que um sistema operacional convidado estiver disponível, você poderá escolher explicitamente essa versão no portal ou no arquivo de configuração.

Para obter uma grande quantidade de informações valiosas sobre reinicializações e ponteiros para obter mais detalhes técnicos sobre as atualizações de sistema operacional convidado e host, consulte a postagem de blog do MSDN chamada [instância de função reinicia devido a atualizações do sistema operacional][restarts].

Se você atualizar manualmente o SO convidado, consulte a política de desativação do [SO convidado][retirepolicy] para obter informações adicionais.

## <a name="guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação do SO convidado
A política de suporte e desativação do SO convidado é explicada [aqui][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[RSS feed de atualização do SO convidado]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
