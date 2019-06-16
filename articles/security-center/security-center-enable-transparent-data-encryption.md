---
title: Ativar a encriptação de dados transparente no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure **ativar a encriptação de dados transparente**.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704037"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Ativar a encriptação de dados transparente no Centro de segurança do Azure
Centro de segurança do Azure recomendará que ativar a encriptação de dados transparente (TDE) no bases de dados SQL se TDE não estiver já ativada. TDE protege os dados e ajuda-o a cumprir os requisitos de conformidade ao encriptar a sua base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso, sem a necessidade de fazer alterações à sua aplicação. Para saber mais, veja [encriptação de dados transparente com a base de dados do Azure SQL](https://msdn.microsoft.com/library/dn948096).

Esta recomendação aplica-se ao serviço do SQL do Azure, apenas; não inclui o SQL em execução nas suas máquinas virtuais.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **ativar a encriptação de dados transparente**.
   ![Ativar a Encriptação de Dados Transparente][1]
2. Esta ação abre o **ativar a encriptação de dados transparente nas bases de dados do SQL** painel. Selecione uma base de dados SQL para ativar a TDE.
   ![Selecione o SQL DB para ativar a TDE no][2]
3. Na **encriptação de dados transparente** painel, selecione **ON** sob a encriptação de dados e selecione **guardar** na faixa de opções superior do painel.
   ![Ativar o TDE][3]

   Assim que a TDE está ativado na base de dados SQL selecionada, o **estado de encriptação** será alterado para **Encrypted**.    

   ![Estado de encriptação][4]

## <a name="see-also"></a>Consulte também
Este artigo mostrou como implementar a recomendação do Centro de segurança "Ativar a encriptação de dados transparente." Para saber mais sobre o SQL TDE, consulte o seguinte:

* [Encriptação de dados transparente com a base de dados SQL do Azure](https://msdn.microsoft.com/library/dn948096)
* [Começar com encriptação de dados transparente (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
