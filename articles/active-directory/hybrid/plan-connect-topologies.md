---
title: 'Azure AD Connect: Topologias com suporte | Microsoft Docs'
description: Este tópico detalha topologias com e sem suporte para Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9618e02f54fbb2a3b92771761c5fcf700d126b5c
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70275218"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias do Azure AD Connect
Este artigo descreve várias topologias locais e Azure Active Directory (Azure AD) que usam Azure AD Connect sincronização como a solução de integração de chaves. Este artigo inclui configurações com e sem suporte.


Aqui está a legenda das imagens no artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta Active Directory local |![Floresta Active Directory local](./media/plan-connect-topologies/LegendAD1.png) |
| Active Directory local com importação filtrada |![Active Directory com importação filtrada](./media/plan-connect-topologies/LegendAD2.png) |
| Servidor de sincronização de Azure AD Connect |![Servidor de sincronização de Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| "Modo de preparo" do servidor de sincronização do Azure AD Connect |!["Modo de preparo" do servidor de sincronização do Azure AD Connect](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync com o Forefront Identity Manager (FIM) 2010 ou Microsoft Identity Manager (MIM) 2016 |![GALSync com FIM 2010 ou MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Azure AD Connect servidor de sincronização, detalhado |![Azure AD Connect servidor de sincronização, detalhado](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Cenário sem suporte |![Cenário sem suporte](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft não oferece suporte à modificação ou operação Azure AD Connect sincronização fora das configurações ou ações documentadas formalmente. Qualquer uma dessas configurações ou ações pode resultar em um estado inconsistente ou sem suporte de Azure AD Connect sincronização. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.


## <a name="single-forest-single-azure-ad-tenant"></a>Única floresta, único locatário do Azure AD
![Topologia para uma única floresta e um único locatário](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma única floresta local, com um ou vários domínios, e um único locatário do Azure AD. Para a autenticação do Azure AD, a sincronização de hash de senha é usada. A instalação expressa do Azure AD Connect dá suporte apenas a essa topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, vários servidores de sincronização para um locatário do Azure AD
![Topologia filtrada sem suporte para uma única floresta](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Ter vários servidores de sincronização Azure AD Connect conectados ao mesmo locatário do Azure AD não tem suporte, exceto para um [servidor de preparo](#staging-server). Ele não tem suporte mesmo se esses servidores estiverem configurados para sincronizar com um conjunto mutuamente exclusivo de objetos. Você pode ter considerado essa topologia se não conseguir acessar todos os domínios na floresta a partir de um único servidor ou se quiser distribuir a carga entre vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, único locatário do Azure AD
![Topologia para várias florestas e um único locatário](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas de Active Directory locais. Há vários motivos para ter mais de uma floresta de Active Directory local. Exemplos típicos são designs com florestas de recursos de conta e o resultado de uma fusão ou aquisição.

Quando você tem várias florestas, todas as florestas devem estar acessíveis por um único servidor de sincronização de Azure AD Connect. O servidor deve ser ingressado em um domínio. Se necessário, para acessar todas as florestas, você pode posicionar o servidor em uma rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede filtrada).

O assistente de instalação do Azure AD Connect oferece várias opções para consolidar usuários que são representados em várias florestas. O objetivo é que um usuário seja representado apenas uma vez no Azure AD. Há algumas topologias comuns que você pode configurar no caminho de instalação personalizada no assistente de instalação. Na página **identificando seus usuários exclusivamente** , selecione a opção correspondente que representa sua topologia. A consolidação é configurada somente para usuários. Grupos duplicados não são consolidados com a configuração padrão.

As topologias comuns são discutidas nas seções sobre topologias separadas, [malha completa](#multiple-forests-full-mesh-with-optional-galsync)e [a topologia de recurso de conta](#multiple-forests-account-resource-forest).

A configuração padrão no Azure AD Connect sincronização pressupõe:

* Cada usuário tem apenas uma conta habilitada e a floresta em que essa conta está localizada é usada para autenticar o usuário. Essa suposição é para a sincronização de hash de senha, a autenticação de passagem e a Federação. UserPrincipalName e sourceAnchor/imutável são provenientes dessa floresta.
* Cada usuário tem apenas uma caixa de correio.
* A floresta que hospeda a caixa de correio de um usuário tem a melhor qualidade de dados para atributos visíveis na GAL (lista de endereços global) do Exchange. Se não houver caixa de correio para o usuário, qualquer floresta poderá ser usada para contribuir com esses valores de atributo.
* Se você tiver uma caixa de correio vinculada, também haverá uma conta em uma floresta diferente usada para entrar.

Se o seu ambiente não corresponder a essas suposições, acontecerá o seguinte:

* Se você tiver mais de uma conta ativa ou mais de uma caixa de correio, o mecanismo de sincronização selecionará uma e ignorará a outra.
* Uma caixa de correio vinculada sem nenhuma outra conta ativa não é exportada para o Azure AD. A conta de usuário não é representada como um membro de qualquer grupo. Uma caixa de correio vinculada no DirSync é sempre representada como uma caixa de correio normal. Essa alteração é intencionalmente um comportamento diferente para dar melhor suporte a cenários de várias florestas.

Você pode encontrar mais detalhes em [noções básicas sobre a configuração padrão](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um locatário do Azure AD
![Topologia sem suporte para várias florestas e vários servidores de sincronização](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Não há suporte para ter mais de um servidor de sincronização de Azure AD Connect conectado a um único locatário do Azure AD. A exceção é o uso de um [servidor de preparo](#staging-server).

Essa topologia é diferente da seguinte, pois não há suporte para **vários servidores de sincronização** conectados a um único locatário do Azure AD.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Várias florestas, servidor de sincronização único, os usuários são representados em apenas um diretório
![Opção para representar usuários apenas uma vez em todos os diretórios](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Representação de várias florestas e topologias separadas](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Nesse ambiente, todas as florestas locais são tratadas como entidades separadas. Nenhum usuário está presente em nenhuma outra floresta. Cada floresta tem sua própria organização do Exchange e não há nenhum GALSync entre as florestas. Essa topologia pode ser a situação após uma fusão/aquisição ou em uma organização em que cada unidade de negócios Opera de forma independente. Essas florestas estão na mesma organização do Azure AD e aparecem com uma GAL unificada. Na figura anterior, cada objeto em cada floresta é representado uma vez no metaverso e agregado no locatário de destino do Azure AD.

### <a name="multiple-forests-match-users"></a>Várias florestas: corresponder usuários
Comum a todos esses cenários, a distribuição e os grupos de segurança podem conter uma mistura de usuários, contatos e entidades de segurança externa (FSPs). FSPs são usados em Active Directory Domain Services (AD DS) para representar membros de outras florestas em um grupo de segurança. Todos os FSPs são resolvidos para o objeto real no Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Várias florestas: malha completa com GALSync opcional
![Opção para usar o atributo de email para correspondência quando existem identidades de usuário em vários diretórios](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Topologia de malha completa para várias florestas](./media/plan-connect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que os usuários e recursos sejam localizados em qualquer floresta. Normalmente, há relações de confiança bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, poderá haver (opcionalmente) uma solução GALSync local. Cada usuário é então representado como um contato em todas as outras florestas. O GALSync é normalmente implementado por meio do FIM 2010 ou do MIM 2016. Azure AD Connect não pode ser usado para GALSync locais.

Nesse cenário, os objetos de identidade são Unidos por meio do atributo de email. Um usuário que tem uma caixa de correio em uma floresta é Unido aos contatos nas outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Várias florestas: floresta de recursos de conta
![Opção para usar os atributos objectSid e msExchMasterAccountSID para correspondência quando existem identidades em vários diretórios](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologia de floresta de recurso de conta para várias florestas](./media/plan-connect-topologies/MultiForestAccountResource.png)

Em uma topologia de floresta de recursos de conta, você tem uma ou mais florestas de *conta* com contas de usuário ativas. Você também tem uma ou mais florestas de *recursos* com contas desabilitadas.

Nesse cenário, uma (ou mais) floresta de recursos confia em todas as florestas de contas. A floresta de recursos normalmente tem um esquema de Active Directory estendido com o Exchange e o Lync. Todos os serviços do Exchange e do Lync, juntamente com outros serviços compartilhados, estão localizados nessa floresta. Os usuários têm uma conta de usuário desabilitada nessa floresta e a caixa de correio está vinculada à floresta da conta.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações sobre topologia
Algumas cargas de trabalho do Office 365 têm determinadas restrições em topologias com suporte:

| Carga de trabalho | Restrições |
| --------- | --------- |
| Exchange Online | Para obter mais informações sobre topologias híbridas com suporte no Exchange Online, consulte implantações [híbridas com várias florestas de Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype para Empresas | Quando você está usando várias florestas locais, há suporte apenas para a topologia de floresta de recurso de conta. Para obter mais informações, consulte [requisitos ambientais para o Skype for Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Se você for uma organização maior, considere usar o recurso [PreferredDataLocation do Office 365](how-to-connect-sync-feature-preferreddatalocation.md) . Ele permite que você defina em qual região do datacenter os recursos do usuário estão localizados.

## <a name="staging-server"></a>Servidor de preparo
![Servidor de preparo em uma topologia](./media/plan-connect-topologies/MultiForestStaging.png)

O Azure AD Connect dá suporte à instalação de um segundo servidor no *modo de preparo*. Um servidor nesse modo lê dados de todos os diretórios conectados, mas não grava nada em diretórios conectados. Ele usa o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados de identidade.

Em um desastre em que o servidor primário falha, você pode fazer failover para o servidor de preparo. Você faz isso no assistente de Azure AD Connect. Esse segundo servidor pode estar localizado em um datacenter diferente porque nenhuma infraestrutura é compartilhada com o servidor primário. Você deve copiar manualmente qualquer alteração de configuração feita no servidor primário para o segundo servidor.

Você pode usar um servidor de preparo para testar uma nova configuração personalizada e o efeito que ela tem em seus dados. Você pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, você poderá tornar o servidor de preparo o servidor ativo e definir o servidor ativo antigo para o modo de preparo.

Você também pode usar esse método para substituir o servidor de sincronização ativo. Prepare o novo servidor e defina-o para o modo de preparo. Verifique se ele está em um estado bom, desabilite o modo de preparo (tornando-o ativo) e desligue o servidor ativo no momento.

É possível ter mais de um servidor de preparo quando você deseja ter vários backups em data centers diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários locatários do Azure AD
É recomendável ter um único locatário no Azure AD para uma organização.
Antes de planejar o uso de vários locatários do Azure AD, consulte o artigo [Gerenciamento de unidades administrativas no Azure ad](../users-groups-roles/directory-administrative-units.md). Ele abrange cenários comuns em que você pode usar um único locatário.

![Topologia para várias florestas e vários locatários](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Há uma relação 1:1 entre um servidor de sincronização de Azure AD Connect e um locatário do Azure AD. Para cada locatário do Azure AD, você precisa de um Azure AD Connect instalação do servidor de sincronização. As instâncias de locatário do Azure AD são isoladas por design. Ou seja, os usuários em um locatário não podem ver os usuários no outro locatário. Se você quiser essa separação, essa é uma configuração com suporte. Caso contrário, você deve usar o único modelo de locatário do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez em um locatário do Azure AD
![Topologia filtrada para uma única floresta](./media/plan-connect-topologies/SingleForestFiltered.png)

Nessa topologia, um servidor de sincronização Azure AD Connect está conectado a cada locatário do Azure AD. Os servidores de sincronização de Azure AD Connect devem ser configurados para filtragem para que cada um tenha um conjunto mutuamente exclusivo de objetos nos quais operar. Você pode, por exemplo, fazer o escopo de cada servidor para um domínio específico ou uma unidade organizacional.

Um domínio DNS pode ser registrado em apenas um único locatário do Azure AD. Os UPNs dos usuários na instância de Active Directory local também devem usar namespaces separados. Por exemplo, na figura anterior, três sufixos de UPN separados são registrados na instância de Active Directory local: contoso.com, fabrikam.com e wingtiptoys.com. Os usuários em cada domínio de Active Directory local usam um namespace diferente.

>[!NOTE]
>A GalSync (sincronização de lista de endereços global) não é feita automaticamente nessa topologia e requer uma implementação personalizada do MIM adicional para garantir que cada locatário tenha uma GAL (lista de endereços global) completa no Exchange Online e no Skype for Business online.


Essa topologia tem as seguintes restrições em cenários com suporte de outra forma:

* Somente um dos locatários do Azure AD pode habilitar um Exchange híbrido com a instância de Active Directory local.
* Os dispositivos Windows 10 podem ser associados a apenas um locatário do Azure AD.
* A opção SSO (logon único) para sincronização de hash de senha e autenticação de passagem pode ser usada com apenas um locatário do Azure AD.

O requisito para um conjunto de objetos mutuamente exclusivo também se aplica ao Write-back. Alguns recursos de write-back não têm suporte com essa topologia porque supõem uma única configuração local. As funcionalidades incluem:

* Write-back de grupo com a configuração padrão.
* Write-back do dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um locatário do Azure AD
![Topologia sem suporte para uma única floresta e vários locatários](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia sem suporte para uma única floresta e vários conectores](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Essas tarefas não têm suporte:

* Sincronizar o mesmo usuário com vários locatários do Azure AD.
* Faça uma alteração de configuração para que os usuários em um locatário do Azure AD apareçam como contatos em outro locatário do Azure AD.
* Modifique Azure AD Connect sincronização para se conectar a vários locatários do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync usando write-back
![Topologia sem suporte para várias florestas e vários diretórios, com GALSync em foco no Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia sem suporte para várias florestas e vários diretórios, com GALSync concentrando-se no local Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Os locatários do Azure AD são isolados por design. Essas tarefas não têm suporte:

* Altere a configuração de Azure AD Connect sincronização para ler dados de outro locatário do Azure AD.
* Exporte os usuários como contatos para outra instância de Active Directory local usando Azure AD Connect sincronização.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com servidor de sincronização local
![GALSync em uma topologia para várias florestas e vários diretórios](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Você pode usar o FIM 2010 ou o MIM 2016 no local para sincronizar usuários (via GALSync) entre duas organizações do Exchange. Os usuários em uma organização aparecem como usuários/contatos externos na outra organização. Essas diferentes instâncias locais de Active Directory podem então ser sincronizadas com seus próprios locatários do Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Para saber como instalar o Azure AD Connect para esses cenários, consulte [instalação personalizada do Azure ad Connect](how-to-connect-install-custom.md).

Saiba mais sobre a configuração de [sincronização de Azure ad Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md).
