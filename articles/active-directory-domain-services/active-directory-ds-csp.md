---
title: Fornecedores de soluções de nuvem de serviços de domínio do Active Directory do Azure para o Azure | Documentos da Microsoft
description: Serviços de domínio do Active Directory do Azure para fornecedores de soluções de Cloud do Azure.
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
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
ms.author: ergreenl
ms.openlocfilehash: 8beba4f66cf24a937eec77e4bfdee2057b417269
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359654"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Serviços de domínio do Azure Active Directory (AD) para fornecedores de soluções de Cloud do Azure (CSP)
Este artigo explica como pode utilizar o Azure AD Domain Services numa subscrição Azure CSP.

## <a name="overview-of-azure-csp"></a>Descrição geral do CSP do Azure
O Azure CSP é um programa para Microsoft Partners e fornece um canal de licença para vários serviços cloud da Microsoft. O Azure CSP permite que os parceiros para gerir vendas, o proprietário da relação de faturação, fornecer suporte técnico e de faturação e ser o ponto de contato único do cliente. Além disso, o Azure CSP fornece um conjunto completo de ferramentas, incluindo um portal self-service e respetivas APIs. Essas ferramentas que os parceiros CSP facilmente aprovisionar e gerir recursos do Azure e fornecem a faturação para os clientes e as suas subscrições.

O [portal do Centro de parceiros](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) age como um ponto de entrada para todos os parceiros CSP do Azure. Fornece capacidades de gestão de clientes, o processamento automatizado e muito mais. Os parceiros CSP do Azure podem utilizar capacidades de centro de parceiros com uma IU baseada na web ou com o PowerShell e várias chamadas de API.

O diagrama seguinte ilustra como o modelo CSP funciona num alto nível. Contoso tem um Azure AD Active Directory. Eles têm uma parceria com a um CSP, que implementa e gere os recursos na respetiva subscrição do Azure CSP. Contoso também poderá ter regulares (diretas) subscrições do Azure, que são faturadas diretamente para a Contoso.

![Descrição geral do modelo de CSP](./media/csp/csp_model_overview.png)

Inquilino de um parceiro CSP tem três grupos de agente especial - administrador agentes, agentes de suporte técnico e os agentes de vendas. Grupo de administração de agentes é atribuído à função de administrador inquilino no diretório do Azure AD da Contoso. Como resultado, um utilizador que pertença ao grupo de agentes do administrador do parceiro CSP tem privilégios de administrador de inquilino no diretório do Azure AD da Contoso. Quando as provisões de parceiro CSP uma subscrição do Azure CSP da Contoso, o respetivo grupo de agentes de admin é atribuída à função de proprietário para essa subscrição. Como resultado, os agentes de administrador do parceiro CSP tem os privilégios necessários para aprovisionar os recursos do Azure como máquinas virtuais, redes virtuais e serviços de domínio do Azure AD em nome de Contoso.

Para obter mais informações, consulte o [descrição geral do Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Vantagens da utilização do Azure AD Domain Services numa subscrição Azure CSP
O Azure AD Domain Services fornece serviços compatíveis do Windows Server AD no Azure, como o LDAP, autenticação de Kerberos/NTLM, associação a um domínio, política de grupo e DNS. Ao longo de décadas, muitos aplicativos criados para trabalhar no AD usando esses recursos. Muitos fornecedores de software independentes (ISVs) tem criar e implementar aplicações no local dos clientes. Esses aplicativos são oneroso para suportar uma vez que o que muitas vezes requer acesso aos diferentes ambientes em que esses aplicativos são implantados. Com subscrições do Azure CSP, tem uma alternativa mais simples com o dimensionamento e flexibilidade do Azure.

O Azure AD Domain Services agora suporta subscrições do Azure CSP. Agora pode implementar a sua aplicação numa subscrição Azure CSP associada ao diretório do Azure AD do seu cliente. Como resultado, os seus funcionários (equipe de suporte) podem gerenciar, administrar e as máquinas virtuais em que a aplicação é implementada utilizando as credenciais empresariais da sua organização do serviço. Além disso, pode aprovisionar um domínio gerido do Azure AD Domain Services para o diretório do seu cliente do Azure AD. A aplicação está ligada ao domínio gerido do seu cliente. Que se baseiam em Kerberos/NTLM, LDAP, por conseguinte, os recursos na sua aplicação ou o [API System. DirectoryServices](/dotnet/api/system.directoryservices) funcionam de forma totalmente integrada no diretório do seu cliente. O cliente final muito beneficiado consumir seu aplicativo como um serviço, sem a necessidade de se preocupar em manter a infraestrutura da que aplicação é implementada no.

Todos de faturação pelos recursos do Azure consumidos em que a subscrição, incluindo o Azure AD Domain Services, é cobrada é com. Mantém o controlo total sobre a relação com o cliente quando se trata de vendas, faturação, suporte técnico etc. Com a flexibilidade da plataforma Azure CSP, uma pequena equipa de suporte a agentes pode servir muitos desses clientes que têm instâncias da sua aplicação implementada.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modelos de implementação do CSP para serviços de domínio do Azure AD
Existem duas formas em que pode utilizar os serviços de domínio do Azure AD com uma subscrição do Azure CSP. Escolha a correta com base em considerações de segurança e simplificar que os seus clientes tiverem.

### <a name="direct-deployment-model"></a>Modelo de implementação direta
Este modelo de implementação, o Azure AD Domain Services está ativada dentro de uma rede virtual que pertence à subscrição do Azure CSP. Agentes de administrador do parceiro CSP tem os seguintes privilégios:
* Privilégios de administrador global no diretório do Azure AD do cliente.
* Privilégios de proprietário da subscrição na subscrição do Azure CSP.

![Modelo de implementação direta](./media/csp/csp_direct_deployment_model.png)

Neste modelo de implementação, os agentes de administrador do fornecedor CSP podem administrar identidades do cliente. Estes agentes de administrador tem a capacidade de aprovisionar novos utilizadores, grupos, adicionar aplicações no diretório do Azure AD do cliente etc. Este modelo de implementação pode ser adequado para as organizações menores que não tem um administrador de identidade dedicado ou preferir para o parceiro CSP administrar identidades em seu nome.


### <a name="peered-deployment-model"></a>Modelo de implementação em modo de peering
Este modelo de implementação, o Azure AD Domain Services está ativada dentro de uma rede virtual que pertença ao cliente - ou seja, uma subscrição do Azure direct pagou pelo cliente. O parceiro CSP, em seguida, pode implementar aplicações numa rede virtual que pertencem a subscrição do CSP do cliente. As redes virtuais, em seguida, podem ser ligadas através de peering de rede virtual do Azure. Como resultado, as cargas de trabalho/aplicações implementadas pelo parceiro CSP na subscrição Azure CSP pode ligar ao domínio gerido do cliente aprovisionado na subscrição do Azure de direta do cliente.

![Modelo de implementação em modo de peering](./media/csp/csp_peered_deployment_model.png)

Este modelo de implementação fornece uma separação de privilégios e permite que os agentes de suporte técnico do parceiro CSP administrar a subscrição do Azure e implementar e gerir os recursos dentro da mesma. No entanto, os agentes de suporte técnico do parceiro CSP não é necessário ter privilégios de administrador global no diretório do Azure AD do cliente. Os administradores de identidade do cliente podem continuar a gerir identidades para sua organização.

Este modelo de implementação pode ser adequado para cenários em que um ISV (fornecedor independente de software) fornece um alojada versão do seu aplicativo no local, que também tem de ligar para o cliente do AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Administrar o Azure AD Domain Services geridos a domínios em subscrições de CSP
Aplicam-se as seguintes considerações importantes quando administrar um domínio gerido numa subscrição Azure CSP:

* **Agentes de administração do CSP podem aprovisionar um domínio gerido com as respetivas credenciais:** Serviços de domínio do Azure AD suporta subscrições do Azure CSP. Por conseguinte, os utilizadores que pertencem ao grupo de agentes de administrador de um parceiro CSP podem aprovisionar um novo domínio gerido do Azure AD Domain Services.

* **CSPs podem criar scripts de criação de novos domínios geridos para seus clientes através do PowerShell:** Ver [como para ativar o Azure AD Domain Services com o PowerShell](active-directory-ds-enable-using-powershell.md) para obter detalhes.

* **Agentes de administração do CSP não podem executar tarefas de gestão contínua no domínio gerido com as respetivas credenciais:** Os utilizadores administradores do CSP não é possível efetuar tarefas de rotina de gestão no domínio gerido com as respetivas credenciais. Estes utilizadores são externos ao diretório do Azure AD do cliente e as respetivas credenciais não estão disponíveis dentro do diretório do Azure AD do cliente. Por conseguinte, os serviços de domínio do Azure AD não tem acesso para os hashes de palavra-passe de Kerberos e NTLM para estes utilizadores. Como resultado, esses utilizadores não podem ser autenticados em domínios de geridos do Azure AD Domain Services.

  > [!WARNING]
  > **Tem de criar uma conta de utilizador no diretório do cliente para executar tarefas de administração em curso no domínio gerido.**
  > Não é possível iniciar sessão no domínio gerido utilizando as credenciais de um utilizador de administrador CSP. Utilize as credenciais de uma conta de utilizador que pertençam ao diretório do Azure AD do cliente para fazer isso. Precisa estas credenciais para tarefas como associar máquinas virtuais para o domínio gerido, administrar o DNS, administrar etc de política de grupo.
  >

* **A conta de utilizador que criou para a administração em curso tem de ser adicionada ao grupo "Administradores do AAD DC":** O grupo "Administradores do AAD DC" tem privilégios para executar determinadas tarefas de administração delegada no domínio gerido. Essas tarefas incluem a configuração de DNS, a criação de unidades organizacionais, administrar a política de grupo etc. Para um parceiro CSP executar tarefas num domínio gerido, uma conta de utilizador tem de ser criada dentro do diretório do Azure AD do cliente. As credenciais para esta conta tem de ser partilhadas com os agentes de administrador do parceiro CSP. Além disso, esta conta de utilizador tem de ser adicionada ao grupo "Administradores do AAD DC" para ativar tarefas de configuração no domínio gerido para ser efetuada utilizando esta conta de utilizador.


## <a name="next-steps"></a>Passos Seguintes
* [Inscrever no programa Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) e começar a criar o seu negócio através do Azure CSP.
* Reveja a lista de [serviços do Azure disponíveis no Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Ativar o Azure AD Domain Services com o PowerShell](active-directory-ds-enable-using-powershell.md)
* [Introdução ao Azure AD Domain Services](active-directory-ds-getting-started.md)
