---
title: Serviços de Domínio Azure AD para fornecedores de soluções em nuvem [ Microsoft Docs
description: Saiba como ativar e gerir os Serviços de Domínio de Diretório Ativo azure geridos por domínios para fornecedores de soluções de nuvem azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80519094"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implantação e gestão de serviços de domínio de diretório ativo azure para fornecedores de soluções de nuvem azure

O Azure Cloud Solution Providers (CSP) é um programa para a Microsoft Partners e fornece um canal de licença para vários serviços de cloud da Microsoft. A Azure CSP permite aos parceiros gerir as vendas, possuir a relação de faturação, fornecer suporte técnico e de faturação, e ser o único ponto de contacto do cliente. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de self-service e APIs acompanhantes. Estas ferramentas permitem aos parceiros da CSP fornecer e gerir facilmente os recursos do Azure, e fornecer faturação para os clientes e suas subscrições.

O [portal Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) é o ponto de entrada para todos os parceiros DoC Azure, e fornece capacidades ricas de gestão de clientes, processamento automatizado e muito mais. Os parceiros Azure CSP podem usar as capacidades do Partner Center utilizando um UI baseado na web ou utilizando o PowerShell e várias chamadas API.

O diagrama seguinte ilustra como o modelo CSP funciona a um nível elevado. Aqui, Contoso tem um inquilino azure Ative Directory (Azure AD). Têm uma parceria com um CSP, que implanta e gere recursos na sua subscrição de CSP Azure. Contoso também pode ter assinaturas regulares (diretas) do Azure, que são faturadas diretamente para Contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O inquilino do parceiro da CSP tem três grupos de agentes especiais - agentes *de administração,* agentes de *helpdesk* e agentes *de vendas.*

O grupo de agentes *administradores* é designado para o cargo de administrador de inquilinos no inquilino Azure AD de Contoso. Como resultado, um utilizador pertencente ao grupo de agentes administradores do parceiro CSP tem privilégios de administrador de inquilinos no inquilino Azure AD de Contoso.

Quando o parceiro csp prevê uma assinatura Azure CSP para Contoso, o seu grupo de agentes administradores é atribuído ao papel de proprietário para essa subscrição. Como resultado, os agentes administrativos do parceiro CSP têm os privilégios necessários para fornecer recursos Azure, tais como máquinas virtuais, redes virtuais e Serviços de Domínio Azure AD em nome de Contoso.

Para mais informações, consulte a visão geral do [CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Benefícios da utilização do Azure AD DS numa subscrição do CSP Azure

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, política de grupo, autenticação LDAP, Kerberos/NTLM que é totalmente compatível com os Serviços de Domínio ativo do Windows Server. Ao longo das décadas, muitas aplicações foram construídas para trabalhar contra a AD usando estas capacidades. Muitos fornecedores de software independentes (ISVs) construíram e implementaram aplicações nas instalações dos seus clientes. Estas aplicações são difíceis de suportar, uma vez que muitas vezes é necessário aceder aos diferentes ambientes onde as aplicações são implementadas. Com subscrições de CSP Azure, tem uma alternativa mais simples com a escala e flexibilidade do Azure.

A Azure AD DS suporta subscrições de CSP Azure. Pode implementar a sua aplicação numa subscrição De CSP Azure ligada ao inquilino Azure AD do seu cliente. Como resultado, os seus colaboradores (pessoal de apoio) podem gerir, administrar e servir os VMs em que a sua aplicação é implementada usando as credenciais corporativas da sua organização.

Também pode implantar um domínio gerido pela Azure AD DS no inquilino Azure AD do seu cliente. A sua aplicação está então ligada ao domínio gerido pelo seu cliente. Capacidades dentro da sua aplicação que dependem da Kerberos / NTLM, LDAP ou do [System.DirectyServices API](/dotnet/api/system.directoryservices) funcionam perfeitamente contra o domínio do seu cliente. Os clientes finais beneficiam de consumir a sua aplicação como serviço, sem precisar de se preocupar em manter a infraestrutura em que a aplicação é implementada.

Toda a faturação dos recursos Azure que consome nessa subscrição, incluindo o Azure AD DS, é-lhe cobrada. Mantém o controlo total sobre a relação com o cliente no que diz respeito a vendas, faturação, suporte técnico, etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipa de agentes de suporte pode servir muitos desses clientes que têm instâncias da sua aplicação implementadas.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelos de implantação de CSP para Azure AD DS

Existem duas formas de utilizar o Azure AD DS com uma subscrição De CSP Azure. Escolha a certa com base nas considerações de segurança e simplicidade que os seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implementação direta

Neste modelo de implementação, o Azure AD DS está ativado dentro de uma rede virtual que pertence à subscrição do Azure CSP. Os agentes administrativos do parceiro csp têm os seguintes privilégios:

* *Privilégios* de administrador global no inquilino azure do cliente.
* *Privilégios do proprietário* de subscrição na subscrição DoC Azure.

![Modelo de implementação direta](./media/csp/csp_direct_deployment_model.png)

Neste modelo de implementação, os agentes de administração do fornecedor cSP podem administrar identidades para o cliente. Estes agentes de administração podem executar tarefas como fornecer novos utilizadores ou grupos, ou adicionar aplicações dentro do inquilino Azure AD do cliente.

Este modelo de implantação pode ser adequado para organizações mais pequenas que não tenham um administrador de identidade dedicado ou prefiram que o parceiro do CSP administrasse identidades em seu nome.

### <a name="peered-deployment-model"></a>Modelo de implantação peered

Neste modelo de implementação, o Azure AD DS está habilitado dentro de uma rede virtual pertencente ao cliente - uma subscrição direta do Azure paga pelo cliente. O parceiro CSP pode implementar aplicações dentro de uma rede virtual pertencente à subscrição de CSP do cliente. As redes virtuais podem então ser conectadas utilizando o peering da rede virtual Azure.

Com esta implementação, as cargas de trabalho ou aplicações implementadas pelo parceiro CSP na subscrição do CSP Azure podem ligar-se ao domínio gerido pelo cliente, provisionado na subscrição direta do Azure do cliente.

![Modelo de implantação peered](./media/csp/csp_peered_deployment_model.png)

Este modelo de implantação proporciona uma separação de privilégios e permite aos agentes de helpdesk do parceiro CSP administrar a subscrição azure e implementar e gerir recursos dentro dele. No entanto, os agentes de helpdesk do parceiro CSP não precisam de ter privilégios de administrador global no diretório Azure AD do cliente. Os administradores de identidade do cliente podem continuar a gerir identidades para a sua organização.

Este modelo de implementação pode ser adequado para cenários em que um ISV fornece uma versão hospedada da sua aplicação no local, que também precisa de se ligar ao Azure AD do cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrar O DS Azure em assinaturas CSP

As seguintes considerações importantes aplicam-se na administração de um domínio gerido numa subscrição de CSP Azure:

* Os **agentes administrativos da CSP podem fornecer um domínio gerido utilizando as suas credenciais:** A Azure AD DS suporta subscrições de CSP Azure. Os utilizadores pertencentes ao grupo de agentes administrativos de um parceiro CSP podem fornecer um novo domínio gerido pelo Azure AD DS.

* **Os CSPs podem escrever a criação de novos domínios geridos para os seus clientes usando o PowerShell:** Veja [como ativar o Azure AD DS utilizando](powershell-create-instance.md) o PowerShell para obter mais detalhes.

* Os **agentes administrativos da CSP não podem executar tarefas de gestão contínuas no domínio gerido utilizando as suas credenciais:** Os utilizadores de administração da CSP não podem executar tarefas de gestão de rotina dentro do domínio gerido usando as suas credenciais. Estes utilizadores são externos ao inquilino da AD Azure do cliente e as suas credenciais não estão disponíveis no inquilino Azure AD do cliente. O Azure AD DS não tem acesso aos hashes de senha kerberos e NTLM para estes utilizadores, pelo que os utilizadores não podem ser autenticados em domínios geridos pela Azure AD DS.

  > [!WARNING]
  > Deve criar uma conta de utilizador dentro do diretório do cliente para executar tarefas de administração em curso no domínio gerido.
  >
  > Não é possível iniciar sessão no domínio gerido utilizando as credenciais de um utilizador de administração csp. Utilize as credenciais de uma conta de utilizador pertencente ao inquilino Azure AD do cliente para o fazer. Precisa destas credenciais para tarefas como a adesão de VMs ao domínio gerido, administração de DNS ou administração da Política de Grupo.

* **A conta de utilizador criada para a administração em curso deve ser adicionada ao grupo de administradores da *AAD DC:* ** O grupo de administradores da *AAD DC* tem privilégios para executar determinadas tarefas de administração delegadas no domínio gerido. Estas tarefas incluem configurar dNS, criar unidades organizacionais e administrar a política de grupo.
    
    Para que um parceiro CSP execute estas tarefas num domínio gerido, deve ser criada uma conta de utilizador dentro do inquilino Azure AD do cliente. As credenciais para esta conta devem ser partilhadas com os agentes administrativos do parceiro da CSP. Além disso, esta conta de utilizador deve ser adicionada ao grupo de *administradores da AAD DC* para permitir que as tarefas de configuração no domínio gerido sejam executadas através desta conta de utilizador.

## <a name="next-steps"></a>Passos seguintes

Para começar, [inscreva-se no programa Azure CSP.](/partner-center/enrolling-in-the-csp-program) Em seguida, pode ativar os Serviços de Domínio Azure AD utilizando [o portal Azure](tutorial-create-instance.md) ou [o Azure PowerShell](powershell-create-instance.md).
