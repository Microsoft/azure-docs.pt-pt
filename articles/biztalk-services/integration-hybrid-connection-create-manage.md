---
title: Criar e gerir ligações híbridas | Documentos da Microsoft
description: Saiba como criar uma ligação híbrida, a ligação de gerir e instalar o Gestor de ligações híbridas. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 0a9197c8c46cc480f5f096f1d2ef98c0d5823f35
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455790"
---
# <a name="create-and-manage-hybrid-connections"></a>Criar e Gerir Ligações Híbridas

> [!IMPORTANT]
> As Ligações Híbridas do BizTalk são desativadas e substituídas por Ligações Híbridas do Serviço de Aplicações. Para obter mais informações, incluindo como gerir as Ligações Híbridas do BizTalk, veja [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) (Ligações Híbridas do Serviço de Aplicações do Azure).

>[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="overview-of-the-steps"></a>Descrição geral dos passos
1. Criar uma ligação híbrida, introduzindo os **nome de anfitrião** ou **FQDN** do recurso no local na sua rede privada.
2. Uma ligação a aplicações web do Azure ou aplicações móveis do Azure para a ligação híbrida.
3. Instalar o Gestor de ligações híbridas no seu recurso no local e ligue-se para a ligação híbrida específico. O portal do Azure fornece uma experiência de clique único para instalar e ligar.
4. Gerir ligações híbridas e as chaves de ligação.

Este tópico lista estes passos. 

> [!IMPORTANT]
> É possível definir um ponto de final de ligação híbrida para um endereço IP. Se utilizar um endereço IP, pode ou não é possível atingir o recurso no local, dependendo do seu cliente. A ligação híbrida depende do cliente a fazer uma pesquisa de DNS. Na maioria dos casos, o **cliente** é o código da aplicação. Se o cliente não efetua uma pesquisa de DNS (ele não tentará resolver o endereço IP, como se fosse um nome de domínio (x.x.x. x)), em seguida, o tráfego não é enviado através da ligação híbrida.
> 
> Por exemplo (pseudocódigo), define **10.4.5.6** como seu host de locais:
> 
> **O seguinte cenário funciona:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on premises host`
> 
> **O seguinte cenário não funciona:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Criar uma ligação híbrida
Pode ser criada uma ligação híbrida [ligações híbridas do Azure App Service](../app-service/app-service-hybrid-connections.md) **ou** usando [APIs de REST de serviços do BizTalk](https://msdn.microsoft.com/library/azure/dn232347.aspx). 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

#### <a name="additional"></a>Adicionais
* É possível criar várias ligações híbridas. Consulte o [os serviços BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md) para o número de ligações permitido. 
* Cada ligação híbrida é criada com um par de cadeias de ligação: Chaves da aplicação que envie e chaves que ESCUTAM locais. Cada par tem um site primário e uma chave secundária. 

## <a name="LinkWebSite"></a>Ligue a sua aplicação Web do serviço de aplicações do Azure ou a aplicação móvel
Para ligar uma aplicação Web ou aplicação móvel no serviço de aplicações do Azure a uma ligação híbrida existente, selecione **utilizar uma ligação híbrida existente** no painel de ligações híbridas. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalar o Gestor de ligações híbridas no local
Depois de criar uma ligação híbrida, instale o Gestor de ligações híbridas no recurso no local. Pode ser transferido a partir de aplicações web do Azure ou de seu BizTalk Service. 

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]
 
[Ligações híbridas do serviço de aplicações Azure](../app-service/app-service-hybrid-connections.md) também é um bom recurso.

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicionais
* Gestor de ligações híbridas pode ser instalado nos seguintes sistemas operativos:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + e Windows Management Framework 4.0 + necessário)
  * Windows Server 2012 (Windows Management Framework 4.0 + necessário)
  * Windows Server 2012 R2
* Depois de instalar o Gestor de ligações híbridas, ocorre o seguinte: 
  
  * A ligação híbrida alojada no Azure é automaticamente configurada para utilizar a cadeia de ligação principal do aplicativo. 
  * O recurso no local é automaticamente configurado para utilizar a cadeia de ligação no local primário.
* O Gestor de ligações híbridas tem de utilizar uma cadeia de ligação no local válido para autorização. As aplicações Web do Azure ou das aplicações móveis, tem de utilizar uma cadeia de ligação de aplicação válida para autorização.
* Pode dimensionar as ligações híbridas através da instalação de outra instância do Gestor de ligações híbridas noutro servidor. Configure o serviço de escuta no local para utilizar o mesmo endereço como o primeiro serviço de escuta no local. Nesta situação, o tráfego é distribuído aleatoriamente (round robin) entre os serviços de escuta do Active Directory no local. 

## <a name="ManageHybridConnection"></a>Gerir ligações híbridas

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Ligações híbridas do serviço de aplicações Azure](../app-service/app-service-hybrid-connections.md) também é um bom recurso.

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Cópia/voltar a gerar as cadeias de ligação híbrida

[!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)] 

[Ligações híbridas do serviço de aplicações Azure](../app-service/app-service-hybrid-connections.md) também é um bom recurso.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Política de grupo de utilização para controlar os recursos no local utilizados por uma ligação híbrida
1. Transfira o [modelos administrativos do Gestor de ligações híbridas](https://www.microsoft.com/download/details.aspx?id=42963).
2. Extraia os ficheiros.
3. No computador que modifica a diretiva de grupo, faça o seguinte:  
   
   * Copiar o. Arquivos ADMX para o *%WINROOT%\PolicyDefinitions* pasta.
   * Copiar o. ADML ficheiros para o *%WINROOT%\PolicyDefinitions\en-us* pasta.

Depois de o copiar, pode utilizar o Editor de diretiva de grupo para alterar a política.

## <a name="next"></a>Seguinte
[Descrição geral de ligações híbridas](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Consultar Também
[API REST para Gerir os BizTalk Services no Microsoft Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Serviços BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md)  
[Criar um Serviço BizTalk](biztalk-provision-services.md)  
[Serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
