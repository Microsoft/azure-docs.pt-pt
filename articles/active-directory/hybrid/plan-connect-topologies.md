---
title: 'Azure AD Connect: Topologias suportadas | Microsoft Docs'
description: Este tópico detalha topologias suportadas e não apoiadas para Azure AD Connect
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
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359837"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologias do Azure AD Connect
Este artigo descreve várias topologias no local e Azure Ative Directory (Azure AD) que utilizam a sincronização Azure AD Connect como a solução-chave de integração. Este artigo inclui configurações suportadas e não apoiadas.


Aqui está a lenda para as fotos no artigo:

| Description | Símbolo |
| --- | --- |
| Floresta de Diretório Ativo no local |![Floresta de Diretório Ativo no local](./media/plan-connect-topologies/legendad1.png) |
| Diretório ativo no local com importação filtrada |![Diretório ativo com importação filtrada](./media/plan-connect-topologies/legendad2.png) |
| Servidor de sincronização Azure Ad Connect |![Servidor de sincronização Azure Ad Connect](./media/plan-connect-topologies/legendsync1.png) |
| Azure AD Connect sync server "modo de encenação" |![Azure AD Connect sync server "modo de encenação"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync com Gestor de Identidade da Vanguarda (FIM) 2010 ou Microsoft Identity Manager (MIM) 2016 |![GALSync com FIM 2010 ou MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Servidor de sincronização Azure AD Connect, detalhado |![Servidor de sincronização Azure AD Connect, detalhado](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Cenário não suportado |![Cenário não suportado](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> A Microsoft não suporta modificar ou operar a sincronização do Azure AD Connect fora das configurações ou ações que estão formalmente documentadas. Qualquer uma destas configurações ou ações pode resultar num estado inconsistente ou não suportado da sincronização Azure AD Connect. Como resultado, a Microsoft não pode fornecer suporte técnico para tais implementações.


## <a name="single-forest-single-azure-ad-tenant"></a>Floresta única, único inquilino AZure AD
![Topologia para uma única floresta e um único inquilino](./media/plan-connect-topologies/singleforestsingledirectory.png)

A topologia mais comum é uma única floresta no local, com um ou vários domínios, e um único inquilino AD Azure. Para a autenticação Azure AD, é utilizada sincronização de hash de palavra-passe. A instalação expressa do Azure AD Connect suporta apenas esta topologia.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Floresta única, múltiplos servidores de sincronização para um inquilino AZure AD
![Topologia não suportada e filtrada para uma única floresta](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Não é suportado vários servidores de sincronização Azure AD Connect ligados ao mesmo inquilino AD AZure, exceto para um [servidor de preparação](#staging-server). Não é suportado mesmo que estes servidores estejam configurados para sincronizar com um conjunto de objetos mutuamente exclusivos. Você poderia ter considerado esta topologia se você não pode alcançar todos os domínios na floresta a partir de um único servidor, ou se você quiser distribuir carga em vários servidores.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Múltiplas florestas, único inquilino AZure AD
![Topologia para múltiplas florestas e um único inquilino](./media/plan-connect-topologies/multiforestsingledirectory.png)

Muitas organizações têm ambientes com múltiplas florestas de Ative Directory. Existem várias razões para ter mais do que uma floresta de Diretório Ativo no local. Exemplos típicos são desenhos com florestas de recursos de conta e o resultado de uma fusão ou aquisição.

Quando tiver múltiplas florestas, todas as florestas devem ser alcançáveis por um único servidor de sincronização Azure AD Connect. O servidor deve ser unido a um domínio. Se necessário para chegar a todas as florestas, pode colocar o servidor numa rede de perímetro (também conhecida como DMZ, zona desmilitarizada e sub-rede rastreada).

O assistente de instalação Azure AD Connect oferece várias opções para consolidar os utilizadores que estão representados em várias florestas. O objetivo é que um utilizador seja representado apenas uma vez em Azure AD. Existem algumas topologias comuns que pode configurar no caminho de instalação personalizado no assistente de instalação. Na página **de identificação única dos seus utilizadores,** selecione a opção correspondente que representa a sua topologia. A consolidação está configurada apenas para os utilizadores. Os grupos duplicados não são consolidados com a configuração predefinida.

As topologias comuns são discutidas nas secções sobre topologias separadas, [malha completa,](#multiple-forests-full-mesh-with-optional-galsync)e [a topologia dos recursos conta.](#multiple-forests-account-resource-forest)

A configuração padrão na sincronização Azure AD Connect pressupõe:

* Cada utilizador tem apenas uma conta ativada, sendo que a floresta onde esta conta se encontra é utilizada para autenticar o utilizador. Esta suposição destina-se a sincronização de haxixe de palavra-passe, autenticação de passagem e federação. UserPrincipalName e sourceAnchor/immutableID vêm desta floresta.
* Cada utilizador tem apenas uma caixa de correio.
* A floresta que acolhe a caixa de correio para um utilizador tem a melhor qualidade de dados para atributos visíveis na Lista de Endereços Globais de Intercâmbio (GAL). Se não houver caixa de correio para o utilizador, qualquer floresta pode ser usada para contribuir com estes valores de atributo.
* Se tiver uma caixa de correio ligada, há também uma conta numa floresta diferente usada para iniciar sing-in.

Se o seu ambiente não corresponder a estes pressupostos, as seguintes coisas acontecem:

* Se tiver mais de uma conta ativa ou mais do que uma caixa de correio, o motor de sincronização escolhe uma e ignora a outra.
* Uma caixa de correio ligada sem outra conta ativa não é exportada para a Azure AD. A conta de utilizador não é representada como membro em nenhum grupo. Uma caixa de correio ligada em DirSync é sempre representada como uma caixa de correio normal. Esta mudança é intencionalmente um comportamento diferente para melhor suportar cenários de múltiplas florestas.

Pode encontrar mais detalhes na [Compreensão da configuração padrão.](concept-azure-ad-connect-sync-default-configuration.md)

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Múltiplas florestas, vários servidores de sincronização para um inquilino AD AZure
![Topologia não suportada para múltiplas florestas e servidores de sincronização múltiplas](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Ter mais de um servidor de sincronização Azure AD Connect ligado a um único inquilino AZure AD não é suportado. A exceção é a utilização de um servidor de [encenação.](#staging-server)

Esta topologia difere da abaixo na medida em que **vários servidores de sincronização ligados** a um único inquilino AD Azure não são suportados.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Múltiplas florestas, servidor de sincronização única, os utilizadores são representados em apenas um diretório
![Opção para representar os utilizadores apenas uma vez em todos os diretórios](./media/plan-connect-topologies/multiforestusersonce.png)

![Representação de múltiplas florestas e topologias separadas](./media/plan-connect-topologies/multiforestseparatetopologies.png)

Neste ambiente, todas as florestas no local são tratadas como entidades separadas. Nenhum utilizador está presente em qualquer outra floresta. Cada floresta tem a sua própria organização de intercâmbio, e não há GALSync entre as florestas. Esta topologia pode ser a situação após uma fusão/aquisição ou numa organização onde cada unidade de negócio opera de forma independente. Estas florestas estão na mesma organização em Azure AD e aparecem com uma GAL unificada. Na imagem anterior, cada objeto em cada floresta é representado uma vez no metaverso e agregado no inquilino AZure AD alvo.

### <a name="multiple-forests-match-users"></a>Múltiplas florestas: utilizadores de correspondência
Comum a todos estes cenários é que os grupos de distribuição e segurança podem conter uma mistura de utilizadores, contactos e principais de segurança estrangeira (FSPs). Os FSPs são utilizados em Serviços de Domínio de Diretório Ativo (DS AD) para representar membros de outras florestas num grupo de segurança. Todos os FSPs são resolvidos com o objeto real em Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Múltiplas florestas: malha completa com GALSync opcional
![Opção para usar o atributo de correio para combinar quando existem identidades de utilizador em vários diretórios](./media/plan-connect-topologies/multiforestusersmail.png)

![Topologia de malha completa para múltiplas florestas](./media/plan-connect-topologies/multiforestfullmesh.png)

Uma topologia de malha completa permite que os utilizadores e recursos sejam localizados em qualquer floresta. Normalmente, existem fundos bidirecionais entre as florestas.

Se o Exchange estiver presente em mais de uma floresta, pode haver (opcionalmente) uma solução GALSync no local. Cada utilizador é então representado como um contacto em todas as outras florestas. A GALSync é comumente implementada através do FIM 2010 ou MIM 2016. O Azure AD Connect não pode ser utilizado para o GALSync no local.

Neste cenário, os objetos de identidade são unidos através do atributo de correio. Um utilizador que tenha uma caixa de correio numa floresta junta-se aos contactos nas outras florestas.

### <a name="multiple-forests-account-resource-forest"></a>Múltiplas florestas: floresta de recursos conta
![Opção para utilizar os atributos ObjectSID e msExchMasterAccountSID para combinar quando existem identidades em vários diretórios](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Topologia florestal de recursos de conta para múltiplas florestas](./media/plan-connect-topologies/multiforestaccountresource.png)

Numa topologia florestal de recursos de conta, você tem uma ou mais florestas *de conta* com contas de utilizador ativas. Também tem uma ou mais florestas *de recursos* com contas com deficiência.

Neste cenário, uma (ou mais) floresta de recursos confia em todas as florestas de contas. A floresta de recursos normalmente tem um esquema de Diretório Ativo alargado com Exchange e Lync. Todos os serviços de Exchange e Lync, juntamente com outros serviços partilhados, estão localizados nesta floresta. Os utilizadores têm uma conta de utilizador desativada nesta floresta, e a caixa de correio está ligada à floresta de conta.

## <a name="microsoft-365-and-topology-considerations"></a>Microsoft 365 e considerações de topologia
Algumas cargas de trabalho da Microsoft 365 têm certas restrições em topologias suportadas:

| Carga de trabalho | Restrições |
| --------- | --------- |
| Exchange Online | Para obter mais informações sobre as topologias híbridas suportadas pelo Exchange Online, consulte [implementações híbridas com múltiplas florestas de Diretório Ativo.](/Exchange/hybrid-deployment/hybrid-with-multiple-forests) |
| Skype para Empresas | Quando se usa várias florestas no local, apenas a topologia florestal de recursos de conta é suportada. Para mais informações, consulte [os requisitos ambientais do Skype para Business Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Se é uma organização maior, então deve considerar utilizar a funcionalidade [DeLocalização de Dados Preferenciais da Microsoft 365.](how-to-connect-sync-feature-preferreddatalocation.md) Permite definir em que região de datacenter os recursos do utilizador estão localizados.

## <a name="staging-server"></a>Servidor de encenação
![Servidor de encenação em uma topologia](./media/plan-connect-topologies/multiforeststaging.png)

O Azure AD Connect suporta a instalação de um segundo servidor no *modo de preparação*. Um servidor neste modo lê dados de todos os diretórios conectados, mas não escreve nada para diretórios conectados. Utiliza o ciclo normal de sincronização e, portanto, tem uma cópia atualizada dos dados de identidade.

Num desastre em que o servidor primário falha, pode falhar no servidor de paragem. Faça isto no assistente Azure AD Connect. Este segundo servidor pode ser localizado num centro de dados diferente porque nenhuma infraestrutura é partilhada com o servidor primário. Tem de copiar manualmente qualquer alteração de configuração efetuada no servidor primário para o segundo servidor.

Pode utilizar um servidor de preparação para testar uma nova configuração personalizada e o efeito que tem nos seus dados. Pode visualizar as alterações e ajustar a configuração. Quando estiver satisfeito com a nova configuração, pode fazer do servidor de paragem o servidor ativo e definir o antigo servidor ativo para o modo de paragem.

Também pode utilizar este método para substituir o servidor de sincronização ativa. Prepare o novo servidor e coloque-o no modo de preparação. Certifique-se de que está em bom estado, desative o modo de paragem (tornando-o ativo) e desligue o servidor atualmente ativo.

É possível ter mais do que um servidor de encenação quando pretende ter várias cópias de segurança em diferentes datacenters.

## <a name="multiple-azure-ad-tenants"></a>Vários inquilinos da AD AZure
Recomendamos ter um único inquilino em Azure AD para uma organização.
Antes de planear utilizar vários inquilinos da AD Azure, veja o artigo [Gestão de unidades administrativas em Azure AD.](../roles/administrative-units.md) Abrange cenários comuns onde se pode usar um único inquilino.

![Topologia para múltiplas florestas e múltiplos inquilinos](./media/plan-connect-topologies/multiforestmultidirectory.png)

Há uma relação 1:1 entre um servidor de sincronização Azure AD Connect e um inquilino da AD Azure. Para cada inquilino AZure AD, você precisa de uma instalação do servidor de sincronização Azure AD Connect. As instâncias de inquilinos da AD AZure são isoladas pelo design. Ou seja, os utilizadores de um inquilino não podem ver os utilizadores no outro inquilino. Se quiser esta separação, esta é uma configuração suportada. Caso contrário, deve usar o modelo único de inquilino Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Cada objeto apenas uma vez em um inquilino AD Azure
![Topologia filtrada para uma única floresta](./media/plan-connect-topologies/singleforestfiltered.png)

Nesta topologia, um servidor de sincronização Azure AD Connect está ligado a cada inquilino AD AZure. Os servidores de sincronização Azure AD Connect devem ser configurados para filtragem de modo a que cada um tenha um conjunto de objetos mutuamente exclusivo para operar. Pode, por exemplo, estender cada servidor a um determinado domínio ou unidade organizacional.

Um domínio DNS pode ser registado em apenas um único inquilino AZure AD. As UPNs dos utilizadores no local de instância Ative Directory também devem utilizar espaços de nome separados. Por exemplo, na imagem anterior, três sufixos UPN separados estão registados no local: contoso.com, fabrikam.com e wingtiptoys.com. Os utilizadores em cada domínio ative diretório usam um espaço de nome diferente.

>[!NOTE]
>A Sincronização global da Lista de Endereços (GalSync) não é feita automaticamente nesta topologia e requer uma implementação adicional personalizada de MIM para garantir que cada inquilino tem uma lista completa de endereços globais (GAL) em Exchange Online e Skype para Business Online.


Esta topologia tem as seguintes restrições em cenários apoiados de outra forma:

* Um máximo de 5 inquilinos do Azure Ative Directory podem ter Exchange Hybrid com o local de diretório ativo. Este cenário é descrito em [setembro de 2020 A Atualização híbrida do assistente de configuração](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* O Assistente de Configuração Híbrida do Servidor de Câmbio deve ser cu18 ou CU7 de 2016 ou mais tarde.
* Cada instância AD Connect Azure deve estar a funcionar numa máquina ligada a domínios.
* O Azure AD Connect deve ser configurado utilizando a opção de filtragem Domain/OU para filtrar os utilizadores do seu diretório no local. A utilização desta opção garante que os utilizadores apareçam apenas num único inquilino online da Exchange.
* Os dispositivos Windows 10 podem ser associados apenas a um inquilino AZure AD.
* A opção de inscrição única (SSO) para sincronização de hash de palavra-passe e autenticação pass-through pode ser usada com apenas um inquilino AZure AD.

A exigência de um conjunto de objetos mutuamente exclusivos também se aplica à gravação. Algumas funcionalidades de writeback não são suportadas com esta topologia porque assumem uma única configuração no local. Essas funcionalidades incluem:

* Writeback de grupo com configuração predefinida.
* Reversão do dispositivo.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Cada objeto várias vezes em um inquilino AD Azure
![Topologia não suportada para uma única floresta e vários inquilinos](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Topologia não suportada para uma única floresta e conectores múltiplos](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Estas tarefas não são apoiadas:

* Sincronia o mesmo utilizador com vários inquilinos AZure AD.
* Faça uma alteração de configuração para que os utilizadores de um inquilino AZure AD apareçam como contactos em outro inquilino da AD Azure.
* Modifique a sincronização Azure AD Connect para ligar a vários inquilinos AZure AD.

### <a name="galsync-by-using-writeback"></a>GALSync usando writeback
![Topologia não suportada para múltiplas florestas e vários diretórios, com GALSync focado no Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Topologia não suportada para múltiplas florestas e vários diretórios, com GALSync focado no Ative Directory no local](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Os inquilinos da AD AZure são isolados pelo design. Estas tarefas não são apoiadas:

* Altere a configuração da sincronização Azure AD Connect para ler dados de outro inquilino AD Azure.
* Exportar os utilizadores como contactos para outro local, instância ative directy, utilizando a sincronização Azure AD Connect.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync com servidor de sincronização no local
![GALSync em topologia para múltiplas florestas e vários diretórios](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

Pode utilizar o FIM 2010 ou o MIM 2016 no local para sincronizar os utilizadores (via GALSync) entre duas organizações de Intercâmbio. Os utilizadores de uma organização aparecem como utilizadores/contactos estrangeiros na outra organização. Estes diferentes casos de Ative Directory podem então ser sincronizados com os seus próprios inquilinos AD Azure.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Usando clientes não autorizados para aceder ao backend Azure AD Connect
![Usando clientes não autorizados para aceder ao backend Azure AD Connect](./media/plan-connect-topologies/other-client-unsupported.png)

O servidor Azure Ative Directory Connect comunica com o Azure Ative Directory através do backend Azure Ative Directory Connect. O único software que pode ser usado para comunicar com este backend é o Azure Ative Directory Connect. Não é suportado para comunicar com o backend Azure Ative Directory Connect utilizando qualquer outro software ou método. 

## <a name="next-steps"></a>Passos seguintes
Para aprender a instalar o Azure AD Connect para estes cenários, consulte [a instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre [a integração das suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
