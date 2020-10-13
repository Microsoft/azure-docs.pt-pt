---
title: Serviços de Domínio AZure AD para Fornecedores de Soluções cloud Microsoft Docs
description: Saiba como ativar e gerir os domínios geridos pelo Azure Ative Directory Domain Services para fornecedores de soluções de nuvem Azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 524e3682e39b6d9153beeef781deeb20e6ea8750
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88749644"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implementação e gestão de serviços de domínio de diretório ativo Azure para fornecedores de soluções de nuvem Azure

Azure Cloud Solution Providers (CSP) é um programa para a Microsoft Partners e fornece um canal de licença para vários serviços na nuvem da Microsoft. A Azure CSP permite que os parceiros gerem as vendas, possuam a relação de faturação, fornecem suporte técnico e de faturação, e são o único ponto de contacto do cliente. Além disso, a Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal de self-service e APIs que o acompanha. Estas ferramentas permitem aos parceiros da CSP fornecer e gerir facilmente os recursos da Azure, e fornecer faturação para os clientes e suas subscrições.

O [portal Partner Center](/partner-center/azure-plan-lp) é o ponto de entrada para todos os parceiros Azure CSP, e fornece capacidades de gestão de clientes ricas, processamento automatizado, e muito mais. Os parceiros Azure CSP podem usar as capacidades do Partner Center utilizando uma UI baseada na web ou utilizando powerShell e várias chamadas API.

O diagrama que se segue ilustra como o modelo CSP funciona a um nível elevado. Aqui, a Contoso tem um inquilino do Azure Ative Directory (Azure AD). Têm uma parceria com uma CSP, que implanta e gere recursos na sua assinatura Azure CSP. Contoso também pode ter subscrições regulares (diretas) Azure, que são faturadas diretamente para Contoso.

![Visão geral do modelo CSP](./media/csp/csp_model_overview.png)

O inquilino do sócio da CSP tem três grupos de agentes especiais - agentes *da Administração,* agentes *da Helpdesk* e agentes *de vendas.*

O grupo de agentes *da Administração* é designado para o papel de administrador de inquilino no inquilino AD de Contoso. Como resultado, um utilizador pertencente ao grupo de agentes administrativos do parceiro da CSP tem privilégios de administração de inquilinos no inquilino Azure AD de Contoso.

Quando o parceiro da CSP prevê uma subscrição da Azure CSP para a Contoso, o seu grupo de agentes administrativos é atribuído ao papel de proprietário para essa subscrição. Como resultado, os agentes administrativos do parceiro CSP têm os privilégios necessários para a prestação de recursos Azure, tais como máquinas virtuais, redes virtuais e Serviços de Domínio AD AZure em nome da Contoso.

Para mais informações, consulte a visão geral da [Azure CSP](/partner-center/azure-plan-lp)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Benefícios da utilização do Azure AD DS numa subscrição do Azure CSP

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, política de grupo, autenticação de LDAP, Kerberos/NTLM que é totalmente compatível com os Serviços de Domínio do Diretor ativo do Windows Server. Ao longo das décadas, muitas aplicações foram construídas para trabalhar contra a AD usando estas capacidades. Muitos fornecedores de software independentes (ISVs) construíram e implementaram aplicações nas instalações dos seus clientes. Estas aplicações são difíceis de suportar, uma vez que muitas vezes é necessário ter acesso aos diferentes ambientes onde as aplicações são implementadas. Com subscrições Azure CSP, você tem uma alternativa mais simples com a escala e flexibilidade do Azure.

AZure AD DS suporta subscrições Azure CSP. Pode implementar a sua aplicação numa assinatura Azure CSP ligada ao inquilino AZure AD do seu cliente. Como resultado, os seus colaboradores (pessoal de apoio) podem gerir, administrar e servir os VMs nos quais a sua aplicação é implementada usando as credenciais corporativas da sua organização.

Também pode implantar um domínio gerido Azure AD DS no inquilino AZure AD do seu cliente. A sua aplicação está então ligada ao domínio gerido pelo seu cliente. As capacidades dentro da sua aplicação que dependem da Kerberos / NTLM, LDAP ou do [System.DirectoryServices API](/dotnet/api/system.directoryservices) funcionam perfeitamente contra o domínio do seu cliente. Os clientes finais beneficiam de consumir a sua aplicação como serviço, sem precisarem de se preocupar em manter a infraestrutura em que a aplicação é implementada.

Toda a faturação dos recursos Azure que consome nessa subscrição, incluindo a Azure AD DS, é cobrada de volta a si. Mantém o controlo total sobre a relação com o cliente quando se trata de vendas, faturação, suporte técnico, etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipa de agentes de apoio pode servir muitos desses clientes que têm instâncias da sua aplicação implementadas.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modelos de implantação CSP para Azure AD DS

Existem duas formas de utilizar o Azure AD DS com uma assinatura Azure CSP. Escolha a certa com base nas considerações de segurança e simplicidade que os seus clientes têm.

### <a name="direct-deployment-model"></a>Modelo de implantação direta

Neste modelo de implementação, o Azure AD DS está ativado numa rede virtual que pertence à subscrição do Azure CSP. Os agentes administrativos do parceiro da CSP têm os seguintes privilégios:

* *Privilégios de administrador* global no inquilino AZure AD do cliente.
* *Privilégios de proprietário* de subscrição na subscrição Azure CSP.

![Modelo de implantação direta](./media/csp/csp_direct_deployment_model.png)

Neste modelo de implantação, os agentes administrativos do prestador de CSP podem administrar identidades para o cliente. Estes agentes administrativos podem executar tarefas como a disponibilização de novos utilizadores ou grupos, ou adicionar aplicações dentro do inquilino Azure AD do cliente.

Este modelo de implementação pode ser adequado para organizações mais pequenas que não têm um administrador de identidade dedicado ou preferem que o parceiro da CSP administro de identidades em seu nome.

### <a name="peered-deployment-model"></a>Modelo de implementação esprevado

Neste modelo de implementação, o Azure AD DS está ativado dentro de uma rede virtual pertencente ao cliente - uma subscrição Azure direta paga pelo cliente. O parceiro CSP pode implementar aplicações dentro de uma rede virtual pertencente à subscrição CSP do cliente. As redes virtuais podem então ser conectadas utilizando o espreitamento da rede virtual Azure.

Com esta implementação, as cargas de trabalho ou aplicações implementadas pelo parceiro CSP na subscrição CSP Azure podem ligar-se ao domínio gerido do cliente a provisionado na subscrição direta do Azure do cliente.

![Modelo de implementação esprevado](./media/csp/csp_peered_deployment_model.png)

Este modelo de implementação proporciona uma separação de privilégios e permite que os agentes de helpdesk do parceiro CSP administram a subscrição do Azure e implementem e gerem recursos dentro dele. No entanto, os agentes de helpdesk do parceiro CSP não precisam de ter privilégios globais de administrador no diretório AD Azure do cliente. Os administradores de identidade do cliente podem continuar a gerir identidades para a sua organização.

Este modelo de implementação pode ser adequado para cenários onde um ISV fornece uma versão hospedada da sua aplicação no local, que também precisa de se ligar ao Azure AD do cliente.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Administrar Azure AD DS em assinaturas CSP

Aplicam-se as seguintes considerações importantes ao administrar um domínio gerido numa subscrição do Azure CSP:

* **Os agentes administrativos da CSP podem providenciar um domínio gerido utilizando as suas credenciais:** AZure AD DS suporta subscrições Azure CSP. Os utilizadores pertencentes ao grupo de agentes administrativos de um parceiro CSP podem providenciar um novo domínio gerido.

* **Os CSPs podem criar novos domínios geridos para os seus clientes utilizando o PowerShell:** Veja [como ativar o Azure AD DS utilizando o PowerShell](powershell-create-instance.md) para obter detalhes.

* **Os agentes administrativos da CSP não podem executar tarefas de gestão contínua no domínio gerido utilizando as suas credenciais:** Os utilizadores de administração CSP não podem executar tarefas de gestão de rotina dentro do domínio gerido usando as suas credenciais. Estes utilizadores são externos ao inquilino AZure AD do cliente e as suas credenciais não estão disponíveis dentro do inquilino AZure AD do cliente. O Azure AD DS não tem acesso às palavras-passe Kerberos e NTLM para estes utilizadores, pelo que os utilizadores não podem ser autenticados em domínios geridos.

  > [!WARNING]
  > Tem de criar uma conta de utilizador dentro do diretório do cliente para executar tarefas de administração em curso no domínio gerido.
  >
  > Não é possível iniciar sinagem no domínio gerido utilizando as credenciais de um utilizador de administração CSP. Utilize as credenciais de uma conta de utilizador pertencente ao inquilino AZure AD do cliente para o fazer. Precisa destas credenciais para tarefas como juntar VMs ao domínio gerido, administrar DNS ou administrar a Política de Grupo.

* **A conta de utilizador criada para a administração em curso deve ser adicionada ao grupo *de administradores da AAD DC:* ** O grupo *de administradores da AAD DC* tem privilégios para executar certas tarefas de administração delegadas no domínio gerido. Estas tarefas incluem configurar o DNS, criar unidades organizacionais e administrar a política de grupo.
    
    Para que um parceiro da CSP execute estas tarefas num domínio gerido, deve ser criada uma conta de utilizador dentro do inquilino AZure AD do cliente. As credenciais desta conta devem ser partilhadas com os agentes administrativos do parceiro da CSP. Além disso, esta conta de utilizador deve ser adicionada ao grupo *de administradores AAD DC* para permitir que as tarefas de configuração no domínio gerido sejam executadas usando esta conta de utilizador.

## <a name="next-steps"></a>Passos seguintes

Para começar, [inscreva-se no programa Azure CSP](/partner-center/enrolling-in-the-csp-program). Em seguida, pode ativar os Serviços de Domínio AZure AD utilizando [o portal Azure](tutorial-create-instance.md) ou [Azure PowerShell](powershell-create-instance.md).