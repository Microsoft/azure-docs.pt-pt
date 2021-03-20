---
title: Azure AD Connect cloud sync mergulho profundo - como funciona
description: Este tópico fornece informações profundas sobre como funciona a sincronização de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e1079ab4a523fbbc99c1e9190aef960b3f0723f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98614318"
---
# <a name="cloud-sync-deep-dive---how-it-works"></a>Cloud sync mergulho profundo - como funciona

## <a name="overview-of-components"></a>Visão geral dos componentes

![Como funciona](media/concept-how-it-works/how-1.png)

A cloud sync é construída em cima dos serviços AD Azure e tem 2 componentes chave:

- **Agente de provisionamento**: O agente de provisionamento em nuvem Azure AD Connect é o mesmo agente que o Workday inbound e construído na mesma tecnologia do lado do servidor que o proxy de aplicações e a Autenticação Pass Through. Requer e apenas ligação de saída e os agentes são atualizados automaticamente. 
- **Serviço de prestação**: Serviço de prestação de serviços iguais ao fornecimento de saída e ao provisionamento de entrada de trabalho que utiliza um modelo baseado em programadores. Em caso de sincronização na nuvem, as alterações são a provisionadas a cada 2 minutos.


## <a name="initial-setup"></a>Configuração inicial
Durante a configuração inicial, são feitas algumas coisas que fazem com que a sincronização da nuvem aconteça.  Esses avisos são: 

- **Durante a instalação do agente:** Configura o agente para os domínios AD a partir do que pretende.  Esta configuração regista os domínios no serviço de identidade híbrida e estabelece uma ligação de saída ao autocarro de serviço que ouve pedidos.
- **Ao ativar o provisionamento**: Selecione o domínio AD e ative o provisionamento que funciona a cada 2 minutos. Opcionalmente, pode desmarcar a sincronização de haxixe de palavra-passe e definir o e-mail de notificação. Também pode gerir a transformação de atributos usando APIs do Microsoft Graph.


## <a name="agent-installation"></a>Instalação do agente
Segue-se um walk-through do que ocorre quando o agente de provisionamento de nuvens está instalado.

- Em primeiro lugar, o Instalador instala os binários do Agente e o Serviço de Agente em execução sob a Conta de Serviço Virtual (SERVIÇO DE REDE\AADProvisioningAgent).  Uma conta de serviço virtual é um tipo especial de conta que não tem senha e é gerida pelo Windows.
- Em seguida, o Instalador inicia o Assistente.
- O Assistente solicitará credenciais AD AZure, autenticará e recuperará um símbolo.
- Em seguida, o assistente pede as credenciais de administradores de domínio da máquina atual.
- Utilizando estas credenciais, a conta de serviço gerida pelo agente geral (GMSA) para este domínio é criada ou localizada e reutilizada se já existir.
- O serviço de agente está agora reconfigurado para funcionar sob a GMSA.
- O assistente pede agora a configuração de domínio juntamente com a Conta de Administração da Empresa (EA)/Domain Admin (DA) para cada domínio que pretende que o agente possa ser reparado.
- A conta GMSA é então atualizada com permissões que lhe permitem o acesso a cada domínio introduzido acima.
- Em seguida, o assistente aciona o registo do agente
- O agente cria um certificado e utiliza o token AD Azure, regista-se e o certificado com o Serviço de Registo de Identidade Híbrida (HIS)
- O Assistente ativa uma chamada de Grupo DeResefonte de Agente. Esta chamada para o SERVIÇO DE Administração HIS destina-se a atribuir o agente a um ou mais domínios AD na configuração HIS.
- O assistente reinicia agora o serviço de agente.
- O agente chama um Serviço Bootstrap no reinício (e a cada 10 minutos seguintes) para verificar se há atualizações de configuração.  O serviço bootstrap valida a identidade do agente.  Também atualiza o último tempo de botas.  Isto é importante porque se os agentes não forem bootstrap, não estão a receber pontos finais atualizados do Service Bus e podem não conseguir receber pedidos. 


## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>O que é Sistema de Gestão de Identidade de Domínio Cruzado (SCIM)?

A [especificação SCIM](https://tools.ietf.org/html/draft-scim-core-schema-01) é uma norma que é usada para automatizar o intercâmbio de informações de identidade de utilizador ou de grupo entre domínios de identidade como Azure AD. O SCIM está a tornar-se o padrão de facto para o provisionamento e, quando usado em conjunto com padrões de federação como o SAML ou o OpenID Connect, fornece aos administradores uma solução baseada em padrões de ponta a ponta para a gestão de acessos.

O agente de provisão de nuvem Azure AD Connect utiliza SCIM com Azure AD para provisão e desprovisionamento de utilizadores e grupos.

## <a name="synchronization-flow"></a>Fluxo de sincronização
![provisionamento ](media/concept-how-it-works/provisioning-4.png) Uma vez instalado o agente e ativado o provisionamento, ocorre o seguinte fluxo.

1.  Uma vez configurado, o serviço de provisionamento Azure AD liga para o serviço híbrido Azure AD para adicionar um pedido ao ônibus de Serviço. O agente mantém constantemente uma ligação de saída ao Service Bus, ouvindo pedidos e recolhe imediatamente o pedido do System for Cross-domain Identity Management (SCIM). 
2.  O agente divide o pedido em consultas separadas com base no tipo de objeto. 
3.  A AD devolve o resultado ao agente e o agente filtra estes dados antes de enviá-los para a Azure AD.  
4.  O agente devolve a resposta do SCIM à Azure AD.  Estas respostas baseiam-se na filtragem que aconteceu dentro do agente.  O agente usa a deteção para filtrar os resultados. 
5.  O serviço de fornecimento escreve as alterações ao Azure AD.
6. Se este é um Delta Sync em oposição a uma sincronização completa, então é usado cookie/marca de água. Novas consultas receberão alterações a partir dessa marca de cookie/água.

## <a name="supported-scenarios"></a>Cenários apoiados:
Os seguintes cenários são suportados para sincronização de nuvem.


- **Cliente híbrido existente com uma nova floresta**: Azure AD Connect sincronização é utilizada para florestas primárias. A sincronização de nuvens é utilizada para o fornecimento de uma floresta AD (incluindo desligada). Para mais informações consulte o tutorial [aqui.](tutorial-existing-forest.md)

 ![Híbrido existente](media/tutorial-existing-forest/existing-forest-new-forest-2.png)
- **Novo cliente híbrido**: Azure AD Connect não é utilizado. A sincronização de nuvens é usada para o fornecimento de uma floresta de AD.  Para mais informações consulte o tutorial [aqui.](tutorial-single-forest.md)
 
 ![Novos clientes](media/tutorial-single-forest/diagram-2.png)

- **Cliente híbrido existente**: Azure AD Connect sincronização é utilizada para florestas primárias. A sincronização da nuvem é pilotada para um pequeno conjunto de utilizadores nas florestas primárias [aqui.](tutorial-existing-forest.md)

 ![Piloto existente](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

Para mais informações, consulte [as topologias apoiadas.](plan-cloud-sync-topologies.md)



## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
