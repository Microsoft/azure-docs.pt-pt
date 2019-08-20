---
title: Azure Active Directory Domain Services para provedores de soluções de nuvem do Azure | Microsoft Docs
description: Azure Active Directory Domain Services para os provedores de soluções de nuvem do Azure.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: dc4ad7d8cf9f3267713fd066fa79a4d9d8ab733f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612978"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Serviços de domínio Azure Active Directory (AD) para provedores de soluções de nuvem do Azure (CSP)
Este artigo explica como você pode usar Azure AD Domain Services em uma assinatura do Azure CSP.

## <a name="overview-of-azure-csp"></a>Visão geral do Azure CSP
O Azure CSP é um programa para parceiros da Microsoft e fornece um canal de licença para vários serviços em nuvem da Microsoft. O Azure CSP permite que os parceiros gerenciem vendas, possuem a relação de cobrança, fornecem suporte técnico e de cobrança e sejam o único ponto de contato do cliente. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de autoatendimento e as APIs que o acompanham. Essas ferramentas permitem que os parceiros do CSP provisionem e gerenciem facilmente recursos do Azure e forneçam cobrança para os clientes e suas assinaturas.

O [portal do Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) atua como um ponto de entrada para todos os parceiros do Azure CSP. Ele fornece recursos avançados de gerenciamento de clientes, processamento automatizado e muito mais. Os parceiros do Azure CSP podem usar os recursos do Partner Center usando uma interface do usuário baseada na Web ou usando o PowerShell e várias chamadas à API.

O diagrama a seguir ilustra como o modelo CSP funciona em um alto nível. A contoso tem um Active Directory do Azure AD. Eles têm uma parceria com um CSP, que implanta e gerencia recursos em sua assinatura do Azure CSP. A contoso também pode ter assinaturas regulares (diretas) do Azure, que são cobradas diretamente na contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O locatário do parceiro CSP tem três grupos especiais de agente – agentes de administração, agentes de assistência técnica e agentes de vendas. O grupo de agentes de administração é atribuído à função de administrador de locatários no diretório do Azure AD da contoso. Como resultado, um usuário que pertence ao grupo de agentes de administração do parceiro CSP tem privilégios de administrador de locatário no diretório do Azure AD da contoso. Quando o parceiro CSP provisiona uma assinatura do Azure CSP para a contoso, seu grupo de agentes admin é atribuído à função de proprietário para essa assinatura. Como resultado, os agentes de administração do parceiro CSP têm os privilégios necessários para provisionar recursos do Azure, como máquinas virtuais, redes virtuais e Azure AD Domain Services em nome da contoso.

Para obter mais informações, consulte [visão geral do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Benefícios do uso de Azure AD Domain Services em uma assinatura do Azure CSP
O Azure AD Domain Services fornece serviços compatíveis com o AD do Windows Server no Azure, como LDAP, autenticação Kerberos/NTLM, ingresso no domínio, diretiva de grupo e DNS. Em décadas, muitos aplicativos foram criados para trabalhar com o AD usando esses recursos. Muitos ISVs (fornecedores independentes de software) têm aplicativos criados e implantados nos locais dos clientes. Esses aplicativos são oneroso para dar suporte, pois isso geralmente requer acesso aos diferentes ambientes em que esses aplicativos são implantados. Com as assinaturas do Azure CSP, você tem uma alternativa mais simples com a escala e a flexibilidade do Azure.

O Azure AD Domain Services agora dá suporte a assinaturas do Azure CSP. Agora você pode implantar seu aplicativo em uma assinatura do Azure CSP vinculada ao diretório do Azure AD do seu cliente. Como resultado, seus funcionários (equipe de suporte) podem gerenciar, administrar e atender às máquinas virtuais nas quais seu aplicativo é implantado usando as credenciais corporativas da sua organização. Além disso, você pode provisionar um Azure AD Domain Services domínio gerenciado para o diretório do Azure AD do seu cliente. Seu aplicativo está conectado ao domínio gerenciado do seu cliente. Portanto, os recursos em seu aplicativo que dependem do Kerberos/NTLM, LDAP ou da [API System. DirectoryServices](/dotnet/api/system.directoryservices) funcionam diretamente no diretório do cliente. Os clientes finais se beneficiam muito do consumo de seu aplicativo como serviço, sem a necessidade de se preocupar em manter a infraestrutura na qual o aplicativo está implantado.

Toda a cobrança dos recursos do Azure que você consome nessa assinatura, incluindo Azure AD Domain Services, é cobrada de volta para você. Você mantém controle total sobre a relação com o cliente quando se trata de vendas, cobrança, suporte técnico etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipe de agentes de suporte pode atender a muitos clientes que têm instâncias do seu aplicativo implantadas.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelos de implantação do CSP para serviços de domínio do Azure AD
Há duas maneiras pelas quais você pode usar Azure AD Domain Services com uma assinatura do Azure CSP. Escolha a correta com base nas considerações de segurança e simplicidade que seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implantação direta
Nesse modelo de implantação, Azure AD Domain Services está habilitada em uma rede virtual que pertence à assinatura do Azure CSP. Os agentes de administração do parceiro CSP têm os seguintes privilégios:
* Privilégios de administrador global no diretório do Azure AD do cliente.
* Privilégios de proprietário da assinatura na assinatura do Azure CSP.

![Modelo de implantação direta](./media/csp/csp_direct_deployment_model.png)

Nesse modelo de implantação, os agentes de administração do provedor CSP podem administrar identidades para o cliente. Esses agentes de Administração têm a capacidade de provisionar novos usuários, grupos, adicionar aplicativos no diretório do Azure AD do cliente, etc. Esse modelo de implantação pode ser adequado para organizações menores que não têm um administrador de identidade dedicado ou preferem que o parceiro CSP administre identidades em seu nome.


### <a name="peered-deployment-model"></a>Modelo de implantação emparelhada
Nesse modelo de implantação, Azure AD Domain Services está habilitado em uma rede virtual que pertence ao cliente, ou seja, uma assinatura direta do Azure paga pelo cliente. O parceiro CSP pode implantar aplicativos em uma rede virtual que pertence à assinatura do CSP do cliente. As redes virtuais podem então ser conectadas usando o emparelhamento de rede virtual do Azure. Como resultado, as cargas de trabalho/aplicativos implantados pelo parceiro CSP na assinatura do Azure CSP podem se conectar ao domínio gerenciado do cliente provisionado na assinatura direta do Azure do cliente.

![Modelo de implantação emparelhada](./media/csp/csp_peered_deployment_model.png)

Esse modelo de implantação fornece uma separação de privilégios e permite que os agentes de assistência técnica do parceiro CSP administrem a assinatura do Azure e implantem e gerenciem recursos dentro dela. No entanto, os agentes de assistência técnica do parceiro CSP não precisam ter privilégios de administrador global no diretório do Azure AD do cliente. Os administradores de identidade do cliente podem continuar a gerenciar identidades para sua organização.

Esse modelo de implantação pode ser adequado para cenários em que um ISV (fornecedor independente de software) fornece uma versão hospedada de seu aplicativo local, que também precisa se conectar ao AD do cliente.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrando Azure AD Domain Services domínios gerenciados em assinaturas do CSP
As seguintes considerações importantes se aplicam ao administrar um domínio gerenciado em uma assinatura do Azure CSP:

* **Os agentes de administração do CSP podem provisionar um domínio gerenciado usando suas credenciais:** Azure AD Domain Services dá suporte a assinaturas do Azure CSP. Portanto, os usuários que pertencem ao grupo de agentes administradores de um parceiro CSP podem provisionar um novo domínio gerenciado Azure AD Domain Services.

* **Os CSPs podem criar scripts de novos domínios gerenciados para seus clientes usando o PowerShell:** Consulte [como habilitar Azure AD Domain Services usando o PowerShell](powershell-create-instance.md) para obter detalhes.

* **Os agentes de administração do CSP não podem executar tarefas de gerenciamento contínuas no domínio gerenciado usando suas credenciais:** Os usuários administradores do CSP não podem executar tarefas de gerenciamento de rotina dentro do domínio gerenciado usando suas credenciais. Esses usuários são externos ao diretório do Azure AD do cliente e suas credenciais não estão disponíveis no diretório do Azure AD do cliente. Portanto, Azure AD Domain Services não tem acesso aos hashes de senha Kerberos e NTLM para esses usuários. Como resultado, esses usuários não podem ser autenticados em Azure AD Domain Services domínios gerenciados.

  > [!WARNING]
  > **Você deve criar uma conta de usuário no diretório do cliente para executar tarefas de administração contínuas no domínio gerenciado.**
  > Você não pode entrar no domínio gerenciado usando as credenciais de um usuário administrador do CSP. Use as credenciais de uma conta de usuário que pertença ao diretório do Azure AD do cliente para fazer isso. Você precisa dessas credenciais para tarefas como unir máquinas virtuais ao domínio gerenciado, administrar o DNS, administrar o Política de Grupo etc.
  >

* **A conta de usuário criada para administração contínua deve ser adicionada ao grupo ' Administradores do controlador de domínio do AAD ':** O grupo ' Administradores de controlador de domínio do AAD ' tem privilégios para executar determinadas tarefas de administração delegadas no domínio gerenciado. Essas tarefas incluem a configuração do DNS, a criação de unidades organizacionais, a administração da diretiva de grupo, etc. Para que um parceiro CSP execute essas tarefas em um domínio gerenciado, uma conta de usuário precisa ser criada no diretório do Azure AD do cliente. As credenciais para essa conta devem ser compartilhadas com os agentes de administração do parceiro CSP. Além disso, essa conta de usuário deve ser adicionada ao grupo "administradores do controlador de domínio do AAD" para permitir que as tarefas de configuração do domínios gerenciados sejam executadas usando essa conta de usuário.


## <a name="next-steps"></a>Passos Seguintes
* [Registre-se no programa Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) e comece a criar negócios por meio do CSP do Azure.
* Examine a lista de [Serviços do Azure disponíveis no Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Ativar o Azure AD Domain Services com o PowerShell](powershell-create-instance.md)
* [Introdução ao Azure AD Domain Services](tutorial-create-instance.md)
