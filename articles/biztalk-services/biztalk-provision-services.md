---
title: Criar os Serviços BizTalk do Azure no Portal do Azure | Microsoft Docs
description: Saiba como aprovisionar ou criar os BizTalk Services do Azure no Portal do Azure; MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: d47aab9e3d083cd7f51b1c3ba613f421fc1777a6
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764103"
---
# <a name="create-biztalk-services-using-the-azure-portal"></a>Criar os BizTalk Services com o Portal do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!TIP]
> Para iniciar sessão no Portal do Azure, deve ter uma conta e uma subscrição do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja [Avaliação Gratuita do Azure](https://go.microsoft.com/fwlink/p/?LinkID=239738).


## <a name="CreateService"></a>Criar um Serviço BizTalk

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Consoante o estado do BizTalk Service, há determinadas operações que não poderão ser concluídas. Para obter uma lista dessas operações, veja o [Gráfico de Estado dos BizTalk Services](biztalk-service-state-chart.md).

## <a name="post-provisioning-steps"></a>Passos pós-aprovisionamento
* [Instalar o certificado num computador local](#InstallCert)
* [Adicionar um certificado pronto para produção](#AddCert)
* [Obter o espaço de nomes do Controlo de Acesso](#ACS)

#### <a name="InstallCert"></a>Instalar o certificado num computador local

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="AddCert"></a>Adicionar um certificado pronto para produção

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

#### <a name="ACS"></a>Obter o espaço de nomes do Controlo de Acesso

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

Quando implementa um projeto dos BizTalk Services a partir do Visual Studio, introduza este espaço de nomes do Controlo de Acesso. O espaço de nomes do Controlo de Acesso é criado automaticamente para o seu BizTalk Service.

Pode utilizar os valores do Controlo de Acesso com qualquer aplicação. Após criar os BizTalk Services do Azure, este espaço de nomes do Controlo de Acesso controla a autenticação com a implementação do seu BizTalk Service. Se pretender alterar a subscrição ou gerir o espaço de nomes, selecione **ACTIVE DIRECTORY** no painel de navegação esquerdo e, em seguida, selecione o seu espaço de nomes. A barra de tarefas lista as suas opções.

Clique em **Gerir** para abrir o Portal de Gestão do Controlo de Acesso. No Portal de Gestão do Controlo de Acesso, o BizTalk Service utiliza as **Identidades do serviço**:  
![Identidades do Serviço ACS no Portal de Gestão do Controlo de Acesso][ACSServiceIdentities]

A identidade do Serviço do Controlo de Acesso é um conjunto de credenciais que permite a autenticação direta das aplicações ou dos clientes com o Controlo de Acesso e a receção de um token.

> [!IMPORTANT]
> O BizTalk Service utiliza **Proprietário** para a identidade predefinida do serviço e o valor **Palavra-passe**. Se utilizar o valor Chave Simétrica em vez do valor Palavra-passe, poderá ocorrer o seguinte erro.<br/><br/>*Não foi possível ligar à conta do Serviço de Gestão do Controlo de Acesso com as credenciais especificadas*
> 
> 

Em [Gerir o Espaço de Nomes do ACS](https://msdn.microsoft.com/library/azure/hh674478.aspx), pode ver uma lista de algumas diretrizes e recomendações.

## <a name="requirements-explained"></a>Requisitos explicados
Estes requisitos não se aplicam à Edição Gratuita.

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>Do que precisa</strong></td>
        <td><strong>Porque precisa</strong></td>
</tr>
<tr>
<td>Subscrição do Azure</td>
<td>A subscrição determina quem pode iniciar sessão no Azure. O Titular da conta cria a subscrição em <a HREF="https://account.windowsazure.com/Subscriptions">Subscrições do Azure</a>.
<br/><br/>
A conta do Azure pode ter várias subscrições e pode ser gerida por qualquer pessoa autorizada. Por exemplo, o titular da conta do Azure cria uma subscrição com o nome <em>BizTalkServiceSubscription</em> e permite aos Administradores do BizTalk da sua empresa (por exemplo, ContosoBTSAdmins@live.com) acederem a esta subscrição. Neste cenário, os Administradores do BizTalk iniciam sessão no Azure e têm todos os direitos de Administrador para todos os serviços alojados na subscrição, incluindo os Serviços BizTalk do Azure. Os Administradores do BizTalk não são titulares da conta do Azure e, por conseguinte, não têm acesso a quaisquer informações de faturação.
<br/><br/>
<a HREF="https://go.microsoft.com/fwlink/p/?LinkID=267577"> Gerir Subscrições e Contas de Armazenamento no Azure</a> apresenta mais informações.
</td>
</tr>
<tr>
<td>Base de Dados SQL do Azure</td>
<td>Armazena as tabelas, as vistas e os procedimentos armazenados utilizados pelo BizTalk Service, incluindo os Dados de controlo.
<br/><br/>
Quando cria um BizTalk Service, pode utilizar um Servidor SQL do Azure existente, uma SQL Database do Azure ou criar automaticamente um novo servidor ou base de dados.
<br/><br/>
O dimensionamento da SQL Database é configurado automaticamente. Normalmente, o dimensionamento predefinido é suficiente para um BizTalk Service. Alterar o dimensionamento terá impactos nos preços. Veja <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=234930">Contas e Faturação na Base de Dados SQL do Azure</a>
<br/><br/>
<strong>Notas</strong>
<br/>
<ul>
<li> Quando cria uma nova Base de Dados e um novo Servidor SQL do Azure, os Serviços do Azure são automaticamente ativados. Os BizTalk Services requerem a ativação dos Serviços do Azure.</li>
<li>Se criar uma nova SQL Database do Azure num Servidor SQL do Azure existente, as regras de firewall do servidor irão manter-se inalteradas. Por conseguinte, é possível que outros Serviços do Azure não tenham acesso às bases de dados do servidor.</li>
</ul>
</td>
</tr>
<tr>
<td>Espaço de nomes do Controlo de Acesso do Azure</td>
<td>É autenticado com o BizTalk Services do Azure. Quando implementa um projeto dos BizTalk Services a partir do Visual Studio, introduza este espaço de nomes do Controlo de Acesso. Quando cria um BizTalk Service, o espaço de nomes do Controlo de Acesso é criado automaticamente.</td>
</tr>

<tr>
<td>Conta de armazenamento do Azure</td>
<td>Permite aceder às tabelas, blobs e filas utilizados pelo BizTalk Service para guardar o seguinte:

<ul>
<li>Ficheiros de registo que monitorizam o BizTalk Service. </li>
<li>Ao criar um contrato X12 ou AS2 entre parceiros, pode ativar a funcionalidade Arquivo para armazenar as propriedades das mensagens. Estes dados são guardados na Conta de armazenamento.</li>
</ul>
<br/>
Quando cria um BizTalk Service, pode utilizar uma Conta de armazenamento existente ou criar automaticamente uma nova.
<br/><br/>
As predefinições do Armazenamento são suficientes para um BizTalk Service.
<br/><br/>
Quando cria uma Conta de armazenamento, são criadas automaticamente uma Chave Primária e uma Chave Secundária. Estas Chaves controlam o acesso à Conta de armazenamento. O BizTalk Service utiliza automaticamente a Chave Primária.
<br/><br/>
Para obter mais informações, veja <a HREF="https://go.microsoft.com/fwlink/p/?LinkID=285671">Armazenamento</a>.
</td>
</tr>

<tr>
<td>Certificado SSL privado</td>
<td>
Quando cria um BizTalk Service do Azure, também é criado um URL HTTPS com o nome do seu BizTalk Service. Este URL é configurado automaticamente para utilizar um certificado autoassinado apenas de desenvolvimento. Para a produção, precisa de um certificado SSL privado.
<br/><br/>
<strong>Informações importantes sobre o Certificado SSL</strong>

<ul>
<li>A data de expiração do certificado tem de ser inferior a cinco anos.</li>
<li>Todos os certificados privados requerem uma palavra-passe. Lembre-se desta palavra-passe e, como uma melhor prática, partilhe-a com os seus administradores.</li>
<li>Os certificados autoassinados são utilizados num ambiente de teste/desenvolvimento. Quando utilizar certificados autoassinados, importe o certificado para o seu arquivo de Certificados Pessoais e para o arquivo de Certificados de Autoridades de Certificação de Raiz Fidedigna.</li>
</ul>
<br/>Quando enviar o pedido de certificado de produção à autoridade de certificação, faculte as seguintes propriedades do certificado:
<br/>

<ul>
<li><strong>Utilização de chave avançada</strong>: No mínimo, os serviços BizTalk do Azure requer autenticação de servidor.</li>
<li><strong>Nome comum</strong>: Introduza o nome de domínio completamente qualificado (FQDN) do seu URL do serviço BizTalk do Azure. Veja <a HREF="#CreateService">Criar um BizTalk Service</a> neste artigo.</li>
</ul>
<br/>
Após criar o BizTalk Service, pode adicionar um novo certificado ou outro diferente.
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>Ligações Híbridas
Quando cria um BizTalk Service do Azure, o separador **Ligações Híbridas** fica disponível:

![Separador Ligações Híbridas][HybridConnectionTab]

As Ligações Híbridas são utilizadas para ligar um site do Azure ou um serviço móvel do Azure a qualquer recurso no local que utilize uma porta TCP estática, como o SQL Server, o MySQL, as APIs Web de HTTP, os Mobile Services e a maioria dos Serviços Web personalizados.  As Ligações Híbridas e o BizTalk Adapter Service são diferentes. O BizTalk Adapter Service é utilizado para ligar os BizTalk Services do Azure a um sistema de Linha de Negócio (LOB) no local.

 Veja [Ligações Híbridas](integration-hybrid-connection-overview.md) para saber mais, incluindo como criar e gerir as Ligações Híbridas.

## <a name="next-steps"></a>Passos Seguintes
Agora que é criado um BizTalk Service, familiarize-se com os diferentes [os serviços BizTalk: Separadores dashboard, monitorizar e dimensionar](biztalk-dashboard-monitor-scale-tabs.md). O BizTalk Service está pronto para as suas aplicações. Para começar a criar aplicações, veja [BizTalk Services do Azure](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Ver também
* [Serviços BizTalk: Gráfico de edições](biztalk-editions-feature-chart.md)<br/>
* [Serviços BizTalk: Gráfico de estado](biztalk-service-state-chart.md)<br/>
* [Serviços BizTalk: Cópia de segurança e restauro](biztalk-backup-restore.md)<br/>
* [Serviços BizTalk: Throttling](biztalk-throttling-thresholds.md)<br/>
* [Serviços BizTalk: Nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md)<br/>
* [Como posso começar a utilizar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Ligações Híbridas](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png
