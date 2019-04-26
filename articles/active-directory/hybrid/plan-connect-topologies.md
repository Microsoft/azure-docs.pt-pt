---
title: 'Azure AD Connect: Suportadas topologias | Documentos da Microsoft'
description: Este tópico fornece detalhes sobre a topologias suportadas e não suportadas para o Azure AD Connect
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
ms.openlocfilehash: 8b1c0d33a7d920f76bcbea6d8d6babc7390003bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383888"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias do Azure AD Connect
Este artigo descreve vários locais e topologias do Azure Active Directory (Azure AD) que utilizam o Azure AD Connect sync como a solução de integração principais. Este artigo inclui configurações suportadas e não suportadas.


Segue-se a legenda de imagens no artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta do Active Directory no local |![Floresta do Active Directory no local](./media/plan-connect-topologies/LegendAD1.png) |
| No local do Active Directory com a importação filtrada |![Active Directory com a importação filtrado](./media/plan-connect-topologies/LegendAD2.png) |
| Servidor de sincronização do Azure AD Connect |![Servidor de sincronização do Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| Servidor de sincronização do Azure AD Connect "modo de teste" |![Servidor de sincronização do Azure AD Connect "modo de teste"](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync com o Forefront Identity Manager (FIM) 2010 ou o Microsoft Identity Manager (MIM) 2016 |![GALSync com o FIM 2010 ou o MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Servidor de sincronização do Azure AD Connect, detalhado |![Servidor de sincronização do Azure AD Connect, detalhado](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Cenário não suportado |![Cenário não suportado](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft não suporta a modificação ou operação de sincronização do Azure AD Connect fora da configurações ou ações anteriormente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado de sincronização do Azure AD Connect. Como resultado, a Microsoft não pode possível fornecer o suporte técnico para implementações deste tipo.


## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, único inquilino do Azure AD
![Topologia para uma única floresta e de um único inquilino](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é um único local de floresta, com um ou vários domínios e um único Azure AD de inquilino. Para a autenticação do Azure AD, é utilizada a sincronização de hash de palavra-passe. Instalação rápida do Azure AD Connect suporta apenas esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, vários servidores de sincronização para um inquilino do Azure AD
![Topologia não suportada, filtrada para uma única floresta](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Não é suportada a vários servidores de sincronização do Azure AD Connect ligados ao mesmo inquilino do Azure AD, exceto para uma [servidor intermediário](#staging-server). Ele tem não suportado, mesmo que estes servidores estão configurados para sincronizar com um conjunto mutuamente exclusivo de objetos. Tenha considerado esta topologia se não é possível atingir todos os domínios na floresta de um servidor único, ou se desejar distribuir a carga por vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Várias florestas, único inquilino do Azure AD
![Topologia para várias florestas e de um único inquilino](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com várias florestas de Active Directory no local. Existem várias razões para ter mais do que uma floresta de Active Directory no local. Os exemplos comuns são designs com florestas de recursos da conta e o resultado de uma fusão ou aquisição.

Se tiver várias florestas, todas as florestas tem de estar acessível por um único servidor de sincronização do Azure AD Connect. Não precisa de associar o servidor a um domínio. Se for necessário para chegar a todas as florestas, pode colocar o servidor numa rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede filtrada).

O Assistente de instalação do Azure AD Connect oferece várias opções para consolidar os utilizadores que são representados em várias florestas. O objetivo é que um utilizador é representado apenas uma vez no Azure AD. Existem algumas topologias comuns que pode configurar no caminho de instalação personalizada do Assistente de instalação. Sobre o **identificar os utilizadores de forma exclusiva** , selecione a opção correspondente que representa a sua topologia. A consolidação é configurada apenas para os utilizadores. Grupos de duplicados não são consolidados com a configuração padrão.

Topologias de comuns são abordadas nas secções sobre topologias separadas, [completa de malha](#multiple-forests-full-mesh-with-optional-galsync), e [a topologia de recurso de conta](#multiple-forests-account-resource-forest).

Assume a configuração predefinida do Azure AD Connect:

* Cada utilizador tem apenas uma conta ativada e a floresta onde está localizada esta conta é utilizada para autenticar o utilizador. Essa suposição destina-se a sincronização de hash de palavra-passe, autenticação pass-through e Federação. UserPrincipalName e sourceAnchor/immutableID provenientes desta floresta.
* Cada utilizador tem apenas uma caixa de correio.
* A floresta que hospeda a caixa de correio para um utilizador tem a melhor qualidade de dados de atributos visíveis na lista Exchange da endereço Global (GAL). Se não houver nenhuma caixa de correio para o utilizador, qualquer floresta pode ser utilizada para contribuir com estes valores de atributo.
* Se tiver uma caixa de correio ligada, existe também uma conta numa floresta diferente utilizada para início de sessão.

Se seu ambiente não corresponder a essas suposições, acontecem os seguintes procedimentos:

* Se tiver mais de uma conta do Active Directory ou de mais do que uma caixa de correio, o motor de sincronização escolhe um e ignora a outra.
* Uma caixa de correio ligada com nenhuma conta de Active Directory não é exportada para o Azure AD. A conta de utilizador não é representada como um membro de nenhum grupo. Uma caixa de correio vinculada no DirSync sempre é representada como uma caixa de correio normal. Esta alteração é intencionalmente um comportamento diferente para melhor suportar os cenários de várias florestas.

Pode encontrar mais detalhes [entender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Várias florestas, vários servidores de sincronização para um inquilino do Azure AD
![Topologia não suportada para várias florestas e de múltiplos servidores de sincronização](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Ter mais de um servidor de sincronização do Azure AD Connect ligado a um único inquilino do Azure AD não é suportado. A exceção é o uso de um [servidor intermediário](#staging-server).

Esta topologia é diferente daquele abaixo em que **múltiplos servidores de sincronização** ligado a um único Azure AD o inquilino não é suportado.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Várias florestas, o único servidor de sincronização, os utilizadores são representados apenas num diretório
![Opção para representar os utilizadores apenas uma vez em todos os diretórios](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Representação de várias florestas e topologias separadas](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Neste ambiente, todas as florestas no local são tratadas como entidades separadas. Nenhum utilizador está presente em qualquer outra floresta. Cada floresta tem sua própria organização do Exchange e não há nenhum GALSync entre as florestas. Esta topologia poderá ser a situação após uma fusão/aquisição ou numa organização em que cada unidade de negócios funciona de forma independente. Estas florestas estão na mesma organização no Azure AD e aparecem com uma GAL unificada. Na imagem anterior, cada objeto em cada floresta é representado uma vez no metaverso e agregado no inquilino de destino do Azure AD.

### <a name="multiple-forests-match-users"></a>Várias florestas: corresponder a utilizadores
Comuns a todos esses cenários é que, distribuição e grupos de segurança podem conter uma mistura de utilizadores, contactos e entidades de segurança externa (FSPs). FSPs são utilizados em serviços de domínio do Active Directory (AD DS) para representar os membros de outras florestas num grupo de segurança. Todos os FSPs são resolvidos para o objeto real no Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Várias florestas: total de malha com GALSync opcional
![Opção para utilizar o atributo de correio para a correspondência quando existem identidades de utilizadores em vários diretórios](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Topologia de malha completa para várias florestas](./media/plan-connect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que os utilizadores e os recursos estejam localizadas em qualquer floresta. Normalmente, existem relações de confiança bidirecionais entre as florestas.

Se o Exchange está presente em mais do que uma floresta, poderão existir (opcionalmente) uma solução de GALSync no local. Todos os usuários, em seguida, é representado como um contacto em todas as outras florestas. GALSync normalmente é implementado através do FIM 2010 ou o MIM 2016. O Azure AD Connect não pode ser utilizado para GALSync no local.

Neste cenário, os objetos de identidade são associados através do atributo de correio. Um utilizador que possua uma caixa de correio numa floresta é associado os contactos nas outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Várias florestas: floresta de recursos da conta
![Opção para utilizar os atributos ObjectSID e msExchMasterAccountSID para efetuar a correspondência quando existem identidades em vários diretórios](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologia de floresta de recursos de conta para várias florestas](./media/plan-connect-topologies/MultiForestAccountResource.png)

Numa topologia de floresta de recursos de conta, tem uma ou mais *conta* florestas com contas de utilizador do Active Directory. Também tem um ou mais *recursos* florestas com contas desativadas.

Neste cenário, uma (ou mais) floresta de recursos confia em todas as florestas de contas. Floresta de recursos, normalmente, tem um esquema expandido do Active Directory com o Exchange e Lync. Serviços de todos os Exchange e o Lync, juntamente com outros serviços compartilhados, estão localizados nesta floresta. Os utilizadores têm uma conta de usuário desabilitadas nesta floresta e a caixa de correio está ligada a floresta de conta.

## <a name="office-365-and-topology-considerations"></a>Office 365 e considerações sobre a topologia
Algumas cargas de trabalho do Office 365 têm determinadas restrições em topologias suportadas:

| Carga de trabalho | Restrições |
| --------- | --------- |
| Exchange Online | Para obter mais informações sobre topologias híbridas suportado pelo Exchange Online, consulte [implementações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx). |
| Skype para Empresas | Quando estiver usando várias florestas no local, é suportada apenas a topologia de floresta de recursos da conta. Para obter mais informações, consulte [requisitos ambientais para o Skype para empresas Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Se for uma organização grande, então deve considerar a utilizar o [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) funcionalidade. Ele permite-lhe definir em cada região do datacenter estão localizados recursos do utilizador.

## <a name="staging-server"></a>Servidor de teste
![Servidor de teste numa topologia](./media/plan-connect-topologies/MultiForestStaging.png)

O Azure AD Connect suporta, instalar um segundo servidor na *modo de teste*. Um servidor neste modo lê dados a partir de todos os diretórios conectados, mas não escrever nada para diretórios conectados. Utiliza o ciclo de sincronização normal e, portanto, tem uma cópia atualizada dos dados de identidade.

Num desastre em que o servidor principal falhar, pode efetuar a ativação pós-falha para o servidor de preparação. Pode fazê-lo no Assistente do Azure AD Connect. Este segundo servidor de pode estar localizado num datacenter diferente porque nenhuma infraestrutura é partilhada com o servidor primário. Tem de copiar manualmente qualquer alteração de configuração efetuada no servidor primário para o segundo servidor.

Pode utilizar um servidor de teste para testar uma nova configuração personalizada e o efeito que tem nos seus dados. Pode pré-visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, pode tornar o servidor de preparação do servidor ativo e definir o antigo servidor Active Directory para o modo de teste.

Também pode utilizar este método para substituir o servidor de sincronização do Active Directory. Prepare o novo servidor e configurá-lo para o modo de teste. Certifique-se de que está em bom estado, desativar (tornando-Active Directory), de modo de teste e encerrar o servidor atualmente ativo.

É possível ter mais de um servidor de teste, quando deseja ter várias cópias de segurança em datacenters diferentes.

## <a name="multiple-azure-ad-tenants"></a>Vários inquilinos do Azure AD
Recomendamos ter um único inquilino no Azure AD para uma organização.
Antes de planeja usar vários inquilinos do Azure AD, consulte o artigo [gestão de unidades administrativas no Azure AD](../users-groups-roles/directory-administrative-units.md). Abrange cenários comuns em que pode usar um único inquilino.

![Topologia para várias florestas e de vários inquilinos](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Existe uma relação de 1:1 entre um servidor de sincronização do Azure AD Connect e de inquilino do Azure AD. Para cada inquilino do Azure AD, terá de uma instalação de servidor de sincronização do Azure AD Connect. As instâncias de inquilino do Azure AD são isoladas por design. Ou seja, os utilizadores num inquilino não é possível ver os utilizadores no outro inquilino. Se desejar essa separação, esta é uma configuração suportada. Caso contrário, deve usar o único modelo do inquilino do Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez no inquilino do Azure AD
![Topologia filtrada para uma única floresta](./media/plan-connect-topologies/SingleForestFiltered.png)

Nesta topologia, um servidor de sincronização do Azure AD Connect está ligado a cada inquilino do Azure AD. Os servidores de sincronização do Azure AD Connect tem de ser configurados para filtragem, para que cada um tem um conjunto mutuamente exclusivo de objetos a trabalhar. Pode, por exemplo, definir o âmbito cada servidor a um determinado domínio ou unidade organizacional.

Um domínio DNS pode ser registado em apenas um único inquilino do Azure AD. Os UPNs dos utilizadores na instância do Active Directory no local também tem de utilizar espaços de nomes separados. Por exemplo, na imagem anterior, três sufixos do UPN separados são registrados na instância do Active Directory no local: contoso.com e fabrikam.com e wingtiptoys.com. Os utilizadores em cada domínio do Active Directory no local, utilize um espaço de nomes diferente.

>[!NOTE]
>Sincronização de lista de endereços global (GalSync) não é feita automaticamente nesta topologia e requer uma implementação de MIM personalizados adicional para garantir que cada inquilino tem uma completa endereço lista GAL (Global) no Exchange Online e o Skype para empresas Online.


Esta topologia tem as seguintes restrições no caso contrário, suporte a cenários:

* Apenas um dos inquilinos do Azure AD pode ativar uma híbrida do Exchange com a instância do Active Directory no local.
* Dispositivos Windows 10 podem ser associados com apenas um inquilino do Azure AD.
* A única início de sessão (SSO) opção para a autenticação de sincronização e a passagem do hash de palavra-passe pode ser utilizada com apenas um inquilino do Azure AD.

O requisito para um conjunto mutuamente exclusivo de objetos também se aplica a repetição de escrita. Algumas funcionalidades de repetição de escrita não são suportadas com esta topologia porque partem do princípio de uma única configuração no local. Estas funcionalidades incluem:

* Repetição de escrita grupo com a configuração predefinida.
* Repetição de escrita do dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes no inquilino do Azure AD
![Topologia não suportada para uma única floresta e de vários inquilinos](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia não suportada para uma única floresta e vários conectores](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Estas tarefas não são suportadas:

* Sincronize o mesmo utilizador a múltiplos inquilinos do Azure AD.
* Fazer uma alteração para que os utilizadores no inquilino do Azure AD um serão apresentados como contatos no outro inquilino do Azure AD de configuração.
* Modificar a sincronização do Azure AD Connect para ligar a vários inquilinos do Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync ao utilizar a repetição de escrita
![Topologia não suportada para várias florestas e de vários diretórios, com GALSync, concentrando-se no Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia não suportada para várias florestas e de vários diretórios, com GALSync com foco no local do Active Directory](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Inquilinos do Azure AD são isolados por design. Estas tarefas não são suportadas:

* Altere a configuração da sincronização do Azure AD Connect para ler dados a partir de outro inquilino do Azure AD.
* Exporte utilizadores como contatos, noutra instância do Active Directory no local através da utilização do Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com o servidor de sincronização no local
![GALSync numa topologia para várias florestas e de vários diretórios](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Pode utilizar o FIM 2010 ou o MIM 2016 no local para sincronizar os utilizadores (por meio de GALSync) entre duas organizações do Exchange. Os utilizadores numa organização aparecem como utilizadores/contatos externos da outra organização. Esses diferentes no local do Active Directory instâncias, em seguida, podem ser sincronizadas com seus próprios inquilinos do Azure AD.

## <a name="next-steps"></a>Passos Seguintes
Para saber como instalar o Azure AD Connect para estes cenários, consulte [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
