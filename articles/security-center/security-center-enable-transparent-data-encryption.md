---
title: Habilitar Transparent Data Encryption na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar a recomendação da central de segurança do Azure para **habilitar o Transparent Data Encryption**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704037"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Habilitar Transparent Data Encryption na central de segurança do Azure
A central de segurança do Azure recomendará que você habilite Transparent Data Encryption (TDE) em bancos de dados SQL se o TDE ainda não estiver habilitado. O TDE protege seus dados e ajuda a atender aos requisitos de conformidade criptografando seu banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Para saber mais, consulte [Transparent Data Encryption com o banco de dados SQL do Azure](https://msdn.microsoft.com/library/dn948096).

Essa recomendação se aplica somente ao serviço do Azure SQL; Não inclui o SQL em execução em suas máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na folha **recomendações** , selecione **habilitar Transparent Data Encryption**.
   ![Ativar a Encriptação de Dados Transparente][1]
2. Isso abre a folha **habilitar Transparent Data Encryption em bancos de dados SQL** . Selecione um banco de dados SQL para habilitar TDE.
   ![Selecione o banco de BD SQL para habilitar TDE][2]
3. Na folha **Transparent Data Encryption** , selecione **ativado** em criptografia de dados e selecione **salvar** na faixa de opções superior da folha.
   ![Ativar TDE][3]

   Quando o TDE estiver habilitado no banco de dados SQL selecionado, o **status da criptografia** será alterado para **criptografado**.    

   ![Estado de encriptação][4]

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação "Habilitar Transparent Data Encryption" da central de segurança. Para saber mais sobre o SQL TDE, consulte o seguinte:

* [Transparent Data Encryption com o banco de dados SQL do Azure](https://msdn.microsoft.com/library/dn948096)
* [Introdução ao Transparent Data Encryption (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias e informações de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
