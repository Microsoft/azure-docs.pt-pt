---
title: Serviços de Domínio Azure AD para fornecedores de soluções em nuvem [ Microsoft Docs
description: Serviços de Domínio de Diretório Ativo Azure para Fornecedores de Soluções De Nuvem Azure.
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
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754429"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Serviços de Domínio de Diretório Ativo Azure (AD) para fornecedores de soluções de nuvem azure (CSP)
Este artigo explica como pode utilizar os Serviços de Domínio Azure AD numa subscrição de CSP Azure.

## <a name="overview-of-azure-csp"></a>Visão geral do Azure CSP
O Azure CSP é um programa para a Microsoft Partners e fornece um canal de licença para vários serviços de cloud da Microsoft. A Azure CSP permite aos parceiros gerir as vendas, possuir a relação de faturação, fornecer suporte técnico e de faturação, e ser o único ponto de contacto do cliente. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de self-service e APIs acompanhantes. Estas ferramentas permitem aos parceiros da CSP fornecer e gerir facilmente os recursos do Azure, e fornecer faturação para os clientes e suas subscrições.

O [portal Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funciona como um ponto de entrada para todos os parceiros Da CSP Azure. Fornece capacidades de gestão de clientes ricas, processamento automatizado e muito mais. Os parceiros Azure CSP podem usar as capacidades do Partner Center utilizando um UI baseado na web ou utilizando o PowerShell e várias chamadas API.

O diagrama seguinte ilustra como o modelo CSP funciona a um nível elevado. Contoso tem um Diretório Ativo Azure AD. Têm uma parceria com um CSP, que implanta e gere recursos na sua subscrição de CSP Azure. Contoso também pode ter assinaturas regulares (diretas) do Azure, que são faturadas diretamente para Contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O inquilino do parceiro da CSP tem três grupos de agentes especiais - agentes de administração, agentes de helpdesk e agentes de vendas. O grupo de agentes administradores é designado para o cargo de administrador de inquilinos no diretório Azure AD de Contoso. Como resultado, um utilizador pertencente ao grupo de agentes administradores do parceiro CSP tem privilégios de administrador de inquilinos no diretório Azure AD de Contoso. Quando o parceiro csp prevê uma assinatura Azure CSP para Contoso, o seu grupo de agentes administradores é atribuído ao papel de proprietário para essa subscrição. Como resultado, os agentes administrativos do parceiro CSP têm os privilégios necessários para fornecer recursos Azure, tais como máquinas virtuais, redes virtuais e Serviços de Domínio Azure AD em nome de Contoso.

Para mais informações, consulte a visão geral do [CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Benefícios da utilização dos Serviços de Domínio Azure AD numa subscrição do CSP Azure
Os Serviços de Domínio Azure AD fornecem serviços compatíveis com o Windows Server AD em Azure, tais como lDAP, aautenticação Kerberos/NTLM, adesão ao domínio, política de grupo e DNS. Ao longo das décadas, muitas aplicações foram construídas para trabalhar contra a AD usando estas capacidades. Muitos fornecedores de software independentes (ISVs) construíram e implementaram aplicações nas instalações dos seus clientes. Estas aplicações são onerosas para apoiar, uma vez que muitas vezes requer acesso aos diferentes ambientes em que estas aplicações são implementadas. Com subscrições de CSP Azure, tem uma alternativa mais simples com a escala e flexibilidade do Azure.

A Azure AD Domain Services suporta agora as subscrições do Azure CSP. Agora pode implementar a sua aplicação numa subscrição De CSP Azure ligada ao diretório Azure AD do seu cliente. Como resultado, os seus colaboradores (pessoal de apoio) podem gerir, administrar e servir as máquinas virtuais em que a sua aplicação é implementada usando as credenciais corporativas da sua organização. Além disso, pode fornecer um domínio gerido pelos Serviços de Domínio Azure AD para o diretório Azure AD do seu cliente. A sua aplicação está ligada ao domínio gerido pelo seu cliente. Portanto, as capacidades dentro da sua aplicação que dependem da Kerberos/NTLM, LDAP ou do [SystemyServices API](/dotnet/api/system.directoryservices) funcionam perfeitamente contra o diretório do seu cliente. Os seus clientes finais beneficiam muito de consumir a sua aplicação como serviço, sem precisar de se preocupar em manter a infraestrutura em que a aplicação está implantada.

Toda a faturação dos recursos Azure que consome nessa subscrição, incluindo os Serviços de Domínio Azure AD, é-lhe cobrada. Mantém o controlo total sobre a relação com o cliente no que diz respeito a vendas, faturação, suporte técnico, etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipa de agentes de suporte pode servir muitos desses clientes que têm instâncias da sua aplicação implementadas.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelos de implantação do CSP para serviços de domínio Azure AD
Existem duas formas de utilizar os Serviços de Domínio Azure AD com uma subscrição De CSP Azure. Escolha a certa com base nas considerações de segurança e simplicidade que os seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implementação direta
Neste modelo de implementação, o Azure AD Domain Services está ativado dentro de uma rede virtual pertencente à subscrição do Azure CSP. Os agentes administrativos do parceiro csp têm os seguintes privilégios:
* Privilégios de administrador global no diretório Azure AD do cliente.
* Privilégios do proprietário de subscrição na subscrição DoC Azure.

![Modelo de implementação direta](./media/csp/csp_direct_deployment_model.png)

Neste modelo de implementação, os agentes de administração do fornecedor cSP podem administrar identidades para o cliente. Estes agentes administrativos têm a capacidade de fornecer novos utilizadores, grupos, adicionar aplicações dentro do diretório Azure AD do cliente, etc. Este modelo de implantação pode ser adequado para organizações mais pequenas que não tenham um administrador de identidade dedicado ou prefiram que o parceiro do CSP administrasse identidades em seu nome.


### <a name="peered-deployment-model"></a>Modelo de implantação peered
Neste modelo de implementação, o Azure AD Domain Services está habilitado dentro de uma rede virtual pertencente ao cliente - ou seja, uma subscrição direta do Azure paga pelo cliente. O parceiro CSP pode então implementar aplicações dentro de uma rede virtual pertencente à subscrição de CSP do cliente. As redes virtuais podem então ser conectadas utilizando o peering da rede virtual Azure. Como resultado, as cargas de trabalho/aplicações implementadas pelo parceiro CSP na subscrição do CSP Azure podem ligar-se ao domínio gerido pelo cliente, provisionado na subscrição direta do Azure do cliente.

![Modelo de implantação peered](./media/csp/csp_peered_deployment_model.png)

Este modelo de implantação proporciona uma separação de privilégios e permite aos agentes de helpdesk do parceiro CSP administrar a subscrição azure e implementar e gerir recursos dentro dele. No entanto, os agentes de helpdesk do parceiro CSP não precisam de ter privilégios de administrador global no diretório Azure AD do cliente. Os administradores de identidade do cliente podem continuar a gerir identidades para a sua organização.

Este modelo de implementação pode ser adequado para cenários em que um ISV (fornecedor de software independente) fornece uma versão hospedada da sua aplicação no local, que também precisa de se ligar ao AD do cliente.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrar serviços de domínio Azure AD geridos domínios em subscrições de CSP
As seguintes considerações importantes aplicam-se na administração de um domínio gerido numa subscrição de CSP Azure:

* Os **agentes administrativos da CSP podem fornecer um domínio gerido utilizando as suas credenciais:** O Azure AD Domain Services suporta subscrições de CSP Azure. Por conseguinte, os utilizadores pertencentes a um grupo de agentes administradores de um parceiro CSP podem fornecer um novo domínio gerido pela Azure AD Domain Services.

* **Os CSPs podem escrever a criação de novos domínios geridos para os seus clientes usando o PowerShell:** Veja como ativar os Serviços de [Domínio Azure AD utilizando](powershell-create-instance.md) o PowerShell para obter mais detalhes.

* **Os agentes administrativos da CSP não podem executar tarefas de gestão contínuas no domínio gerido utilizando as suas credenciais:** Os utilizadores de administração da CSP não podem executar tarefas de gestão de rotina dentro do domínio gerido utilizando as suas credenciais. Estes utilizadores são externos ao diretório azure ad do cliente e as suas credenciais não estão disponíveis no diretório azure do cliente. Por isso, os Serviços de Domínio Da Azure AD não têm acesso às hashes de senha kerberos e NTLM para estes utilizadores. Como resultado, estes utilizadores não podem ser autenticados em domínios geridos pela Azure AD Domain Services.

  > [!WARNING]
  > **Deve criar uma conta de utilizador dentro do diretório do cliente para executar tarefas de administração em curso no domínio gerido.**
  > Não é possível iniciar sessão no domínio gerido utilizando as credenciais de um utilizador de administração csp. Utilize as credenciais de uma conta de utilizador pertencente ao diretório Azure AD do cliente para o fazer. Precisa destas credenciais para tarefas como a junção de máquinas virtuais ao domínio gerido, a administração de DNS, a administração de Política de Grupo, etc.
  >

* **A conta de utilizador criada para a administração em curso deve ser adicionada ao grupo "Administradores aAD DC":** O grupo 'AAD DC Administrators' tem privilégios para executar determinadas tarefas de administração delegadas no domínio gerido. Estas tarefas incluem a configuração de DNS, a criação de unidades organizacionais, a administração da política do grupo, etc. Para que um parceiro CSP execute tais tarefas num domínio gerido, uma conta de utilizador precisa de ser criada dentro do diretório Azure AD do cliente. As credenciais para esta conta devem ser partilhadas com os agentes administrativos do parceiro da CSP. Além disso, esta conta de utilizador deve ser adicionada ao grupo 'Administradores AAD DC' para permitir que as tarefas de configuração no domínio gerido sejam executadas através desta conta de utilizador.


## <a name="next-steps"></a>Passos seguintes
* [Inscreva-se no programa Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) e comece a criar negócioatravés do Azure CSP.
* Reveja a lista de [serviços azure disponíveis no Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Ativar o Azure AD Domain Services com o PowerShell](powershell-create-instance.md)
* [Introdução ao Azure AD Domain Services](tutorial-create-instance.md)
