---
title: Proteger serviços de dados e armazenamento do Azure no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento trata recomendações no Centro de segurança do Azure que o ajudam a proteger os seus dados e o serviço SQL do Azure e mantenha-se em conformidade com as políticas de segurança.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275290"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Proteger serviços de dados e armazenamento do Azure no Centro de segurança do Azure
Este tópico mostra-lhe como ver e implementar recomendações de segurança para os recursos de dados e armazenamento. Foram encontrados estas recomendações do Centro de segurança do Azure ao analisar o estado de segurança dos seus recursos do Azure.

## <a name="view-your-data-security-information"></a>Veja as suas informações de segurança de dados

1. Na **higiene de troca de segurança de recursos** secção, clique em **recursos de dados e armazenamento**.

   ![Recursos de dados e armazenamento](./media/security-center-monitoring/click-data.png)

    O **segurança de dados** página abre-se com recomendações para os recursos de dados.

     ![Recursos de Dados](./media/security-center-monitoring/sql-overview.png)

Nesta página, pode:

* Clique nas **descrição geral** separador lista todas as recomendações de recursos de dados para ser resolvido. 
* Clique em cada separador e ver as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre a encriptação de armazenamento, leia o artigo [Ativar a encriptação para a conta de armazenamento do Azure no Centro de Segurança do Azure](security-center-enable-encryption-for-storage-account.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Remediar uma recomendação num recurso de dados

1. A partir de qualquer uma das guias de recursos, clique num recurso. A página de informações é aberta com as recomendações para ser resolvido.

    ![Informações de recursos](./media/security-center-monitoring/sql-recommendations.png)

2. Clique numa recomendação. Página de recomendações será aberta e exibirá os **passos de remediação** para implementar a recomendação.

   ![Passos de remediação](./media/security-center-monitoring/remediate1.png)

3. Clique em **agir**. É apresentada a página de definições de recursos.

    ![Ativar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga os **passos de remediação** e clique em **guardar**.

## <a name="data-and-storage-recommendations"></a>Recomendações de armazenamento e dados

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Conta de armazenamento|20|Deve ser ativada para contas de armazenamento com transferência segura|Transferência segura é uma opção que força a sua conta de armazenamento para aceitar pedidos apenas a partir de ligações seguras (HTTPS). HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, tais como o man-in-the-middle, interceptação e seqüestro de sessão.|
|Redis|20|Devem ser ativadas apenas ligações seguras para a Cache de Redis|Permitir apenas ligações através de SSL para o Azure Cache de Redis. Utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, tais como o man-in-the-middle, interceptação e seqüestro de sessão.|
|SQL|15|Deve ser ativada a encriptação de dados transparente nas bases de dados SQL|Ative a encriptação de dados transparente proteger dados em repouso e cumprir os requisitos de conformidade.|
|SQL|15|Auditoria do SQL server deve ser ativada|Ative a auditoria para servidores SQL do Azure. (Apenas para serviços do SQL do azure. Não inclui SQL em execução nas suas máquinas virtuais.)|
|O Data lake analytics|5|Os registos de diagnóstico no Data Lake Analytics devem ser ativados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Arquivo do Data lake|5|Os registos de diagnóstico do Azure Data Lake Store devem ser ativados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|SQL|30|Vulnerabilidades nos seus bancos de dados do SQL devem ser resolvidas|Avaliação de vulnerabilidades do SQL analisa a sua base de dados para vulnerabilidades de segurança e expõe qualquer desvios das melhores práticas, como configurações incorretas, permissões excessivas e dados confidenciais não protegidos. Resolver as vulnerabilidades encontradas pode melhorar muito o escritor de segurança da base de dados.|
|SQL|20|Aprovisionar um administrador do Azure AD para o SQL server|Aprovisione um administrador do Azure AD para o servidor SQL para ativar a autenticação do Azure AD. Autenticação do Azure AD permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.|
|Conta de armazenamento|15|Acesso a contas de armazenamento com firewall e as configurações de rede virtual deve ser restrito|Auditar o acesso de rede sem restrições nas definições de firewall da conta de armazenamento. Em vez disso, configure regras de rede para que apenas aplicativos de redes permitidas podem acessar a conta de armazenamento. Para permitir ligações de Internet específico ou de clientes no local, pode conceder acesso para o tráfego a partir de redes virtuais do Azure específicos ou para intervalos de endereços IP de Internet públicos.|
|Conta de armazenamento|1|Contas de armazenamento devem ser migradas para novos recursos do Azure Resource Manager|Utilizar o novo Azure Resource Manager v2 para as suas contas de armazenamento para fornecer aprimoramentos de segurança, tais como: aceder a mais forte controlo de acesso (RBAC), a auditoria melhor, a implantação baseada no Resource Manager e a governação de identidades geridas, acesso ao Cofre de chaves para segredos e a autenticação baseada no AD do Azure e o suporte para etiquetas e grupos de recursos para facilitar a gestão segurança.|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes tópicos:

* [Proteger as máquinas virtuais no Centro de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger as aplicações no Centro de Segurança do Azure](security-center-application-recommendations.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre o Centro de segurança, consulte os seguintes tópicos:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
