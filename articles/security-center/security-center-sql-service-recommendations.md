---
title: Proteger os serviços de armazenamento e dados do Azure na central de segurança do Azure | Microsoft Docs
description: Este documento aborda as recomendações na central de segurança do Azure que ajudam a proteger seus dados e o serviço SQL do Azure e manter a conformidade com as políticas de segurança.
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
ms.author: v-mohabe
ms.openlocfilehash: c2adf9bff4c3d91305e71a7688740fa0268ad7b9
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531476"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Proteger os serviços de armazenamento e dados do Azure na central de segurança do Azure
Este tópico mostra como exibir e implementar recomendações de segurança para dados e recursos de armazenamento. A central de segurança do Azure encontrou essas recomendações ao analisar o estado de segurança de seus recursos do Azure.

## <a name="view-your-data-security-information"></a>Exibir suas informações de segurança de dados

1. Na seção **higiene de segurança de recursos** , clique em **dados e recursos de armazenamento**.

   ![Recursos de armazenamento de & de dados](./media/security-center-monitoring/click-data.png)

    A página **segurança de dados** é aberta com recomendações para recursos de dados.

     ![Recursos de Dados](./media/security-center-monitoring/sql-overview.png)

Nessa página, você pode:

* Clique na guia **visão geral** para listar todas as recomendações de recursos de dados a serem corrigidas. 
* Clique em cada guia e exiba as recomendações por tipo de recurso.

    > [!NOTE]
    > Para obter mais informações sobre criptografia de armazenamento, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Corrigir uma recomendação em um recurso de dados

1. Em qualquer uma das guias de recurso, clique em um recurso. A página informações é aberta, listando as recomendações a serem corrigidas.

    ![Informações do recurso](./media/security-center-monitoring/sql-recommendations.png)

2. Clique em uma recomendação. A página recomendação é aberta e exibe as **etapas de correção** para implementar a recomendação.

   ![Passos de remediação](./media/security-center-monitoring/remediate1.png)

3. Clique em **executar ação**. A página Configurações de recurso é exibida.

    ![Habilitar recomendação](./media/security-center-monitoring/remediate2.png)

4. Siga as **etapas de correção** e clique em **salvar**.

## <a name="data-and-storage-recommendations"></a>Recomendações de armazenamento e dados

|Tipo de recurso|Classificação de segurança|Recomendação|Descrição|
|----|----|----|----|
|Conta de armazenamento|20|A transferência segura para contas de armazenamento deve ser habilitada|Transferência segura é uma opção que força sua conta de armazenamento a aceitar solicitações somente de conexões seguras (HTTPS). O HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|
|Redis|20|Somente conexões seguras para o cache Redis devem ser habilitadas|Habilite somente as conexões via SSL para o cache do Azure para Redis. O uso de conexões seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camada de rede, como Man-in-the-Middle, espionagem e seqüestro de sessão.|
|SQL|15|Transparent Data Encryption em bancos de dados SQL devem ser habilitadas|Ative a encriptação transparente de dados para proteger dados inativos e requisitos de conformidade.|
|SQL|15|A auditoria do SQL Server deve ser habilitada|Habilite a auditoria para servidores SQL do Azure. (Somente serviço do Azure SQL. Não inclui o SQL em execução em suas máquinas virtuais.)|
|Análise data Lake|5|Os logs de diagnóstico no Data Lake Analytics devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|Data Lake Store|5|Os logs de diagnóstico no Azure Data Lake Store devem ser habilitados|Ativar os registos e retenha-los até um ano. Isto permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida. |
|SQL|30|Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas|A avaliação de vulnerabilidade do SQL examina seu banco de dados quanto a vulnerabilidades de segurança e expõe quaisquer desvios das práticas recomendadas, como configurações incorretas, excesso de permissões e dados confidenciais não protegidos. A resolução das vulnerabilidades encontradas pode melhorar muito o estatura de segurança do banco de dados.|
|SQL|20|Aprovisionar um administrador do Azure AD para o SQL Server|Provisione um administrador do Azure AD para o SQL Server para habilitar a autenticação do Azure AD. A autenticação do Azure AD permite o gerenciamento de permissões simplificado e o gerenciamento de identidade centralizado de usuários de banco de dados e outros serviços da Microsoft.|
|Conta de armazenamento|15|O acesso a contas de armazenamento com firewall e configurações de rede virtual deve ser restrito|Auditar o acesso irrestrito à rede nas configurações de firewall da sua conta de armazenamento. Em vez disso, configure as regras de rede para que somente aplicativos de redes permitidas possam acessar a conta de armazenamento. Para permitir conexões de clientes locais ou da Internet específicos, você pode conceder acesso ao tráfego de redes virtuais específicas do Azure ou a intervalos de endereços IP de Internet públicos.|
|Conta de armazenamento|1|As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager|Use o New Azure Resource Manager V2 para suas contas de armazenamento para fornecer aprimoramentos de segurança, como: controle de acesso mais forte (RBAC), melhor auditoria, implantação e governança baseadas no Resource Manager, acesso a identidades gerenciadas, acesso ao cofre de chaves para segredos e autenticação baseada no Azure AD e suporte para marcas e grupos de recursos para facilitar o gerenciamento da segurança.|

## <a name="see-also"></a>Consulte também
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes tópicos:

* [Proteger as máquinas virtuais no Centro de Segurança do Azure](security-center-virtual-machine-recommendations.md)
* [Proteger as aplicações no Centro de Segurança do Azure](security-center-application-recommendations.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)

Para saber mais sobre a central de segurança, consulte os seguintes tópicos:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
