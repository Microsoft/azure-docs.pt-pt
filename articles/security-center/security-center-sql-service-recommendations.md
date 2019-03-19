---
title: Proteger o serviço SQL do Azure e os dados no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento trata recomendações no Centro de segurança do Azure que o ajudam a proteger os seus dados e o serviço SQL do Azure e mantenha-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 86d50092ccffd5ab958df5d54ea49cd56d815e2e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082165"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Proteger o serviço SQL do Azure e os dados no Centro de segurança do Azure
Centro de segurança do Azure analisa o estado de segurança dos seus recursos do Azure. Quando o Centro de segurança identifica potenciais vulnerabilidades de segurança, cria recomendações que descreve o processo de configuração de controlos necessários.  Recomendações se aplicam a tipos de recursos do Azure: máquinas virtuais (VMs), redes, SQL e dados e aplicações.


### <a name="monitor-data-security"></a>Monitorizar segurança de dados

Quando clica em **Segurança de dados** na secção **Prevenção**, é aberto **Recursos de Dados** com recomendações para SQL e Armazenamento. Também tem [recomendações](security-center-sql-service-recommendations.md) para o estado de funcionamento geral da base de dados. Para obter mais informações sobre a encriptação de armazenamento, leia o artigo [Ativar a encriptação para a conta de armazenamento do Azure no Centro de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).

![Recursos de Dados](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Em **Recomendações SQL**, pode clicar em qualquer recomendação e obter mais detalhes sobre mais ações para resolver um problema. O exemplo seguinte mostra a expansão da recomendação **Auditoria de Base de Dados e Deteção de ameaças em bases de dados SQL**.

![Detalhes sobre uma recomendação de SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

**Ativar Auditoria e Deteção de ameaças nas bases de dados SQL** tem as seguintes informações:

* Uma lista de bases de dados SQL
* O servidor no qual estão localizadas
* Informações sobre se esta definição foi herdada a partir do servidor ou se é exclusiva desta base de dados
* O estado atual
* A gravidade do problema

Quando clica na base de dados para abordar esta recomendação, é aberto **Deteção de Ameaças e Auditoria**, conforme mostrado no ecrã seguinte.

![Auditoria e Deteção de Ameaças](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Para ativar a auditoria, selecione **ATIVAR**, na opção **Auditoria**.

## <a name="data-and-storage-recommendations"></a>Recomendações de armazenamento e dados

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Conta de armazenamento|20|Exigir a transferência segura para a conta de armazenamento|Transferência segura é uma opção que força a sua conta de armazenamento para aceitar pedidos apenas a partir de ligações seguras (HTTPS). Utilização de HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, tais como o man-in-the-middle, interceptação e seqüestro de sessão.|
|Redis|20|Ativar ligações apenas seguras para a sua Cache do Azure para Redis|Permitir apenas ligações através de SSL para o Azure Cache de Redis. Utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, tais como o man-in-the-middle, interceptação e seqüestro de sessão.|
|SQL|15|Ativar a encriptação de dados transparente nas bases de dados SQL|Ative a encriptação transparente de dados para proteger dados inativos e requisitos de conformidade.|
|SQL|15|Ativar a auditoria em servidores SQL|Ative a auditoria para servidores SQL do Azure. (Apenas para serviços do SQL do azure. Não inclui SQL em execução nas suas máquinas virtuais.)|
|SQL|15|Ativar a auditoria em bases de dados SQL|Ative a auditoria para bases de dados SQL do Azure. (Apenas para serviços do SQL do azure. Não inclui SQL em execução nas suas máquinas virtuais.)|
|O Data lake analytics|15|Ativar a encriptação em repouso do Data Lake Analytics|Ative a encriptação de dados transparente proteger os dados Inativos no seu Data Lake Analytics. Encriptação inativa é transparente, o que significa que o Data Lake Analytics encripta automaticamente os dados antes de persistir e desencripta-os antes da obtenção. Não foram efetuadas alterações necessárias nos aplicativos em e serviços que interagem com o Data Lake Analytics devido à encriptação. Encriptação em repouso minimiza o risco de perda de dados contra roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|Arquivo do Data lake|15|Ativar a encriptação em repouso para o Store de Lake de dados|Ative a encriptação de dados transparente proteger os dados Inativos no Store de Lake seus dados. Encriptação inativa é transparente, o que significa que o Store de Lake dados encripta automaticamente os dados antes de persistir e desencripta-os antes da obtenção. Não precisa de fazer qualquer alteração nas aplicações e serviços que interagem com o Data Lake Store para acomodar a encriptação. Encriptação em repouso minimiza o risco de perda de dados contra roubo físico e também ajuda a atender aos requisitos de conformidade a normas.|
|O Data lake analytics|5|Ativar os registos de diagnóstico no Data Lake Analytics|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Arquivo do Data lake|5|Ativar os registos de diagnóstico no Data Lake Store do Azure|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|SQL|30|Remediar vulnerabilidades nas suas bases de dados SQL|Avaliação de vulnerabilidades do SQL analisa a sua base de dados para vulnerabilidades de segurança e expõe qualquer desvios das melhores práticas, como configurações incorretas, permissões excessivas e dados confidenciais não protegidos. Resolver as vulnerabilidades encontradas pode melhorar muito o escritor de segurança da base de dados.|
|SQL|20|Aprovisionar um administrador do Azure AD para o SQL Server|Aprovisione um administrador do Azure AD para o servidor SQL para ativar a autenticação do Azure AD. Autenticação do Azure AD permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.|
|Conta de armazenamento|15|Desativar acesso de rede sem restrições à conta de armazenamento|Auditar o acesso de rede sem restrições nas definições de firewall da conta de armazenamento. Em vez disso, configure regras de rede para que apenas aplicativos de redes permitidas podem acessar a conta de armazenamento. Para permitir ligações a partir da Internet específico ou os clientes locais, o acesso pode ser concedido para tráfego a partir de redes virtuais do Azure específicos ou para intervalos de endereços IP de Internet públicos.|
|Conta de armazenamento|1|Migrar as contas de armazenamento para os novos recursos do AzureRM|Utilizar o novo Azure Resource Manager v2, para as suas contas de armazenamento fornecer aprimoramentos de segurança, tais como: aceder a mais forte controlo de acesso (RBAC), a auditoria melhor, a implantação baseada no Resource Manager e a governação de identidades geridas, acesso ao Cofre de chaves para segredos, autenticação baseada no AD do Azure e o suporte para etiquetas e grupos de recursos para facilitar a gestão segurança.|



## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte o seguinte:

* [Proteger as máquinas virtuais no Centro de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger as aplicações no Centro de Segurança do Azure](security-center-application-recommendations.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
