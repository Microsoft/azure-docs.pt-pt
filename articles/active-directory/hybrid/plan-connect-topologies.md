---
title: 'Azure AD Connect: Topoologias suportadas Microsoft Docs'
description: Este tópico detalha topoologias suportadas e não suportadas para Azure AD Connect
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253835"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias do Azure AD Connect
Este artigo descreve várias topologias de Diretório Ativo Azure (Azure AD) que utilizam a sincronização Azure AD Connect como a solução chave de integração. Este artigo inclui configurações suportadas e não suportadas.


Aqui está a lenda para imagens no artigo:

| Descrição | Símbolo |
| --- | --- |
| Floresta de Diretório Ativo no local |![Floresta de Diretório Ativo no local](./media/plan-connect-topologies/LegendAD1.png) |
| No local, diretório ativo com importação filtrada |![Diretório ativo com importação filtrada](./media/plan-connect-topologies/LegendAD2.png) |
| Servidor de sincronização Azure AD Connect |![Servidor de sincronização Azure AD Connect](./media/plan-connect-topologies/LegendSync1.png) |
| "Modo de encenação" do servidor de sincronização Azure AD Connect |!["Modo de encenação" do servidor de sincronização Azure AD Connect](./media/plan-connect-topologies/LegendSync2.png) |
| GALSync com Gestor de Identidade De Vanguarda (FIM) 2010 ou Microsoft Identity Manager (MIM) 2016 |![GALSync com FIM 2010 ou MIM 2016](./media/plan-connect-topologies/LegendSync3.png) |
| Servidor de sincronização Azure AD Connect, detalhado |![Servidor de sincronização Azure AD Connect, detalhado](./media/plan-connect-topologies/LegendSync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/LegendAAD.png) |
| Cenário não apoiado |![Cenário não apoiado](./media/plan-connect-topologies/LegendUnsupported.png) |


> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar o Sincronde Azure AD Connect fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado de sincronização Azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.


## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, inquilino único da AD Azure
![Topologia para uma única floresta e um único inquilino](./media/plan-connect-topologies/SingleForestSingleDirectory.png)

A topologia mais comum é uma única floresta no local, com um ou vários domínios, e um único inquilino azure aD. Para autenticação AD Azure, é utilizada sincronização de hash de palavra-passe. A instalação expressa do Azure AD Connect suporta apenas esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, múltiplos servidores de sincronização para um inquilino da AD Azure
![Topologia filtrada e não suportada para uma única floresta](./media/plan-connect-topologies/SingleForestFilteredUnsupported.png)

Não é suportado ter vários servidores de sincronização Azure AD Connect ligados ao mesmo inquilino Azure AD, exceto um servidor de [encenação](#staging-server). Não é suportado mesmo que estes servidores estejam configurados para sincronizar com um conjunto mutuamente exclusivo de objetos. Pode ter considerado esta topologia se não conseguir alcançar todos os domínios da floresta a partir de um único servidor, ou se quiser distribuir carga por vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Múltiplas florestas, inquilino único da AD Azure
![Topologia para múltiplas florestas e um único inquilino](./media/plan-connect-topologies/MultiForestSingleDirectory.png)

Muitas organizações têm ambientes com múltiplas florestas de Diretório Ativo no local. Existem várias razões para ter mais do que uma floresta de Diretório Ativo no local. Exemplos típicos são desenhos com florestas de recursos de conta e o resultado de uma fusão ou aquisição.

Quando se tem várias florestas, todas as florestas devem ser acessíveis por um único servidor de sincronização Azure AD Connect. O servidor deve ser unido a um domínio. Se necessário para chegar a todas as florestas, pode colocar o servidor numa rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede rastreada).

O assistente de instalação Azure AD Connect oferece várias opções para consolidar os utilizadores que estão representados em várias florestas. O objetivo é que um utilizador esteja representado apenas uma vez em Azure AD. Existem algumas topologias comuns que pode configurar no caminho de instalação personalizado no assistente de instalação. Na página de identificação única dos **seus utilizadores,** selecione a opção correspondente que representa a sua topologia. A consolidação está configurada apenas para os utilizadores. Os grupos duplicados não são consolidados com a configuração predefinida.

As topoologias comuns são discutidas nas secções sobre topoologias separadas, [malha completa,](#multiple-forests-full-mesh-with-optional-galsync)e [a topologia dos recursos de conta.](#multiple-forests-account-resource-forest)

A configuração predefinida no sincronizado Azure AD Connect assume:

* Cada utilizador tem apenas uma conta ativada, e a floresta onde esta conta está localizada é utilizada para autenticar o utilizador. Esta suposição é para a sincronização de hash de senha, autenticação pass-through e federação. UserPrincipalName e fonteAnchor/imutávelID provêm desta floresta.
* Cada utilizador tem apenas uma caixa de correio.
* A floresta que acolhe a caixa de correio para um utilizador tem a melhor qualidade de dados para atributos visíveis na Lista de Endereços Globais de Intercâmbio (GAL). Se não houver caixa de correio para o utilizador, qualquer floresta pode ser usada para contribuir com estes valores de atributo.
* Se você tem uma caixa de correio ligada, há também uma conta em uma floresta diferente usada para iniciar sessão.

Se o seu ambiente não corresponder a estes pressupostos, as seguintes coisas acontecem:

* Se tiver mais do que uma conta ativa ou mais do que uma caixa de correio, o motor sincronizado escolhe um e ignora o outro.
* Uma caixa de correio ligada sem outra conta ativa não é exportada para a AD Azure. A conta de utilizador não está representada como membro em nenhum grupo. Uma caixa de correio ligada no DirSync é sempre representada como uma caixa de correio normal. Esta mudança é intencionalmente um comportamento diferente para melhor apoiar cenários de múltiplas florestas.

Pode encontrar mais detalhes em [Compreender a configuração predefinida](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Múltiplas florestas, múltiplos servidores de sincronização para um inquilino da AD Azure
![Topologia não suportada para múltiplas florestas e múltiplos servidores de sincronização](./media/plan-connect-topologies/MultiForestMultiSyncUnsupported.png)

Não é suportado ter mais de um servidor de sincronização Azure AD Connect ligado a um único inquilino da AD Azure. A exceção é a utilização de um servidor de [encenação.](#staging-server)

Esta topologia difere da que está a seguir, na medida em que não são suportados **múltiplos servidores de sincronização ligados** a um único inquilino da AD Azure.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Múltiplas florestas, servidor de sincronização único, utilizadores estão representados em apenas um diretório
![Opção para representar os utilizadores apenas uma vez em todos os diretórios](./media/plan-connect-topologies/MultiForestUsersOnce.png)

![Representação de múltiplas florestas e topoologias separadas](./media/plan-connect-topologies/MultiForestSeparateTopologies.png)

Neste ambiente, todas as florestas no local são tratadas como entidades separadas. Nenhum utilizador está presente em qualquer outra floresta. Cada floresta tem a sua própria organização Exchange, e não há GALSync entre as florestas. Esta topologia pode ser a situação após uma fusão/aquisição ou numa organização em que cada unidade de negócio opera de forma independente. Estas florestas estão na mesma organização em Azure AD e aparecem com uma GAL unificada. Na imagem anterior, cada objeto em cada floresta é representado uma vez no metaverso e agregado no inquilino da AD Azure alvo.

### <a name="multiple-forests-match-users"></a>Múltiplas florestas: utilizadores de correspondência
Comum a todos estes cenários é que os grupos de distribuição e segurança podem conter uma mistura de utilizadores, contactos e diretores de segurança externa (FSPs). Os FSPs são utilizados em Serviços de Domínio de Diretório Ativo (AD DS) para representar membros de outras florestas num grupo de segurança. Todos os FSPs estão resolvidos com o verdadeiro objeto em Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Múltiplas florestas: malha completa com GALSync opcional
![Opção para utilizar o atributo de correio para combinar quando as identidades dos utilizadores existem em vários diretórios](./media/plan-connect-topologies/MultiForestUsersMail.png)

![Topologia de malha completa para várias florestas](./media/plan-connect-topologies/MultiForestFullMesh.png)

Uma topologia de malha completa permite que os utilizadores e recursos estejam localizados em qualquer floresta. Geralmente, há confiança simesmo entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, pode haver (opcionalmente) uma solução GALSync no local. Cada utilizador é então representado como um contacto em todas as outras florestas. GalSync é comumente implementado através de FIM 2010 ou MIM 2016. O Azure AD Connect não pode ser utilizado para o GALSync no local.

Neste cenário, os objetos de identidade são unidos através do atributo do correio. Um utilizador que tenha uma caixa de correio numa floresta junta-se aos contactos nas outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Múltiplas florestas: floresta de recursos de conta
![Opção para utilizar os atributos ObjectSID e MsExchMasterAccountSID para correspondência quando existem identidades em vários diretórios](./media/plan-connect-topologies/MultiForestUsersObjectSID.png)

![Topologia da floresta de recursos de conta para várias florestas](./media/plan-connect-topologies/MultiForestAccountResource.png)

Numa topologia florestal de recursos de conta, tem uma ou mais *florestas* de conta com contas de utilizador ativas. Você também tem uma ou mais florestas de *recursos* com contas de deficientes.

Neste cenário, um (ou mais) recursos florestais confia em todas as florestas de conta. A floresta de recursos normalmente tem um esquema de Diretório Ativo alargado com Exchange e Lync. Todos os serviços de Intercâmbio e Lync, juntamente com outros serviços partilhados, estão localizados nesta floresta. Os utilizadores têm uma conta de utilizador deficiente nesta floresta, e a caixa de correio está ligada à floresta de conta.

## <a name="office-365-and-topology-considerations"></a>Gabinete 365 e considerações de topologia
Algumas cargas de trabalho do Office 365 têm certas restrições em topologias suportadas:

| Carga de trabalho | Restrições |
| --------- | --------- |
| Exchange Online | Para obter mais informações sobre topoologias híbridas suportadas pelo Exchange Online, consulte [implantações híbridas com múltiplas florestas de Diretório Ativo.](https://technet.microsoft.com/library/jj873754.aspx) |
| Skype para Empresas | Quando se está a usar várias florestas no local, apenas a topologia da floresta de recursos de conta é apoiada. Para mais informações, consulte [os requisitos ambientais para o Skype para o Business Server 2015](https://technet.microsoft.com/library/dn933910.aspx). |

Se é uma organização maior, então deve considerar utilizar a funcionalidade [Office 365 PreferredDataLocation.](how-to-connect-sync-feature-preferreddatalocation.md) Permite-lhe definir em que região do datacenter os recursos do utilizador estão localizados.

## <a name="staging-server"></a>Servidor de encenação
![Servidor de encenação em uma topologia](./media/plan-connect-topologies/MultiForestStaging.png)

O Azure AD Connect suporta a instalação de um segundo servidor no *modo de paragem*. Um servidor neste modo lê dados de todos os diretórios conectados, mas não escreve nada para diretórios conectados. Utiliza o ciclo normal de sincronização e, portanto, tem uma cópia atualizada dos dados de identidade.

Num desastre em que o servidor primário falha, pode falhar no servidor de encenação. Faça isto no assistente Azure AD Connect. Este segundo servidor pode ser localizado num centro de dados diferente porque nenhuma infraestrutura é partilhada com o servidor principal. Tem de copiar manualmente qualquer alteração de configuração feita no servidor primário para o segundo servidor.

Pode utilizar um servidor de encenação para testar uma nova configuração personalizada e o efeito que tem nos seus dados. Pode pré-visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, pode tornar o servidor de encenação no servidor ativo e definir o antigo servidor ativo para o modo de realização.

Também pode utilizar este método para substituir o servidor de sincronização ativa. Prepare o novo servidor e coloque-o no modo de preparação. Certifique-se de que está em bom estado, desative o modo de paragem (tornando-o ativo) e desligue o servidor atualmente ativo.

É possível ter mais do que um servidor de encenação quando se quer ter várias cópias de segurança em diferentes datacenters.

## <a name="multiple-azure-ad-tenants"></a>Vários inquilinos da AD Azure
Recomendamos ter um único inquilino em Azure AD para uma organização.
Antes de pretender utilizar vários inquilinos da AD Azure, consulte o artigo Gestão de [unidades administrativas em Azure AD](../users-groups-roles/directory-administrative-units.md). Abrange cenários comuns onde pode usar um único inquilino.

![Topologia para múltiplas florestas e vários inquilinos](./media/plan-connect-topologies/MultiForestMultiDirectory.png)

Há uma relação 1:1 entre um servidor de sincronização Azure AD Connect e um inquilino da AD Azure. Para cada inquilino da AD Azure, precisa de uma instalação de servidor de sincronização Azure AD Connect. As instâncias de inquilinos da AD Azure são isoladas por design. Ou seja, os utilizadores de um inquilino não podem ver os utilizadores no outro inquilino. Se quiser esta separação, esta é uma configuração suportada. Caso contrário, deverá utilizar o modelo único de inquilino Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez em um inquilino Azure AD
![Topologia filtrada para uma única floresta](./media/plan-connect-topologies/SingleForestFiltered.png)

Nesta topologia, um servidor de sincronização Azure AD Connect está ligado a cada inquilino azure AD. Os servidores de sincronização Azure AD Connect devem ser configurados para filtrar de modo a que cada um tenha um conjunto mutuamente exclusivo de objetos para operar. Pode, por exemplo, examinar cada servidor a um determinado domínio ou unidade organizacional.

Um domínio DNS pode ser registado em apenas um único inquilino Azure AD. As UPNs dos utilizadores no local do Diretório Ativo devem também utilizar espaços de nome separados. Por exemplo, na imagem anterior, três sufixos upn separados estão registados no local de diretório ativo: contoso.com, fabrikam.com e wingtiptoys.com. Os utilizadores de cada domínio de Diretório Ativo no local utilizam um espaço de nome diferente.

>[!NOTE]
>A Sincronização da Lista de Endereços Globais (GalSync) não é feita automaticamente nesta topologia e requer uma implementação adicional de MIM personalizada para garantir que cada inquilino tem uma Lista de Endereços Globais completa (GAL) em Exchange Online e Skype para Business Online.


Esta topologia tem as seguintes restrições em cenários apoiados de outra forma:

* Apenas um dos inquilinos da AD Azure pode permitir um exchange híbrido com a instância de Diretório Ativo no local.
* Os dispositivos Windows 10 podem ser associados apenas a um inquilino da Azure AD.
* A opção de inscrição única (SSO) para sincronização de hash de senha e autenticação pass-through pode ser usada com apenas um inquilino Azure AD.

A exigência de um conjunto de objetos mutuamente exclusivo supõe-se também na reapreciação. Algumas funcionalidades de redação não são suportadas com esta topologia porque assumem uma configuração única no local. As funcionalidades incluem:

* Reescrita do grupo com configuração predefinida.
* Reeminserção do dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um inquilino da AD Azure
![Topologia não suportada para uma única floresta e vários inquilinos](./media/plan-connect-topologies/SingleForestMultiDirectoryUnsupported.png) ![Topologia não suportada para uma única floresta e múltiplos conectores](./media/plan-connect-topologies/SingleForestMultiConnectorsUnsupported.png)

Estas tarefas não são apoiadas:

* Sincronize o mesmo utilizador a vários inquilinos da Azure AD.
* Faça uma alteração de configuração para que os utilizadores de um inquilino DaD Azure apareçam como contactos em outro inquilino da AD Azure.
* Modificar a sincronização Azure AD Connect para ligar a vários inquilinos da AD Azure.

### <a name="galsync-by-using-writeback"></a>GALSync usando reemindo
![Topologia não suportada para múltiplas florestas e múltiplos diretórios, com GALSync focado em Azure AD](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![Topologia não suportada para múltiplas florestas e múltiplos diretórios, com GALSync focado no diretório ativo no local](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Os inquilinos da Azure AD estão isolados pelo design. Estas tarefas não são apoiadas:

* Altere a configuração da sincronização Azure AD Connect para ler dados de outro inquilino da AD Azure.
* Exportar utilizadores como contactos para outra instância de Diretório Ativo no local utilizando o sincronizado Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com servidor de sincronização no local
![GALSync em topologia para múltiplas florestas e múltiplos diretórios](./media/plan-connect-topologies/MultiForestMultiDirectoryGALSync.png)

Pode utilizar o FIM 2010 ou o MIM 2016 no local para sincronizar os utilizadores (via GALSync) entre duas organizações de Intercâmbio. Os utilizadores de uma organização aparecem como utilizadores/contactos estrangeiros na outra organização. Estes diferentes casos de Diretório Ativo no local podem então ser sincronizados com os seus próprios inquilinos da AD Azure.

## <a name="next-steps"></a>Passos seguintes
Para aprender a instalar o Azure AD Connect para estes cenários, consulte a [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre [a integração das suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
