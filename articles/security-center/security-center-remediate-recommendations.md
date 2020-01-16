---
title: Corrigir recomendações na central de segurança do Azure | Microsoft Docs
description: Este artigo explica como corrigir recomendações na central de segurança do Azure para proteger seus recursos e obedecer às políticas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 325c68e5e4531e5519596bea00c370c26460a8ed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981901"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Corrigir recomendações na central de segurança do Azure

As recomendações fornecem sugestões sobre como proteger melhor seus recursos. Implemente uma recomendação seguindo as etapas de correção fornecidas na recomendação.

## Etapas de correção<a name="remediation-steps"></a>

Depois de examinar todas as recomendações, decida qual delas corrigir primeiro. Recomendamos que você use o [impacto de Pontuação segura](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na seção **etapas de correção** . Cada recomendação tem seu próprio conjunto de instruções. A captura de tela a seguir mostra as etapas de correção para configurar aplicativos para permitir somente o tráfego por HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Depois de concluído, uma notificação será exibida informando se a correção foi bem-sucedida.

## Correção de correção rápida<a name="one-click"></a>

A correção rápida permite que você corrija rapidamente uma recomendação em vários recursos. Ele só está disponível para recomendações específicas. A correção rápida simplifica a correção e permite que você aumente rapidamente sua pontuação segura, melhorando a segurança do seu ambiente.

Para implementar a correção rápida de correção:

1. Na lista de recomendações que têm a **correção rápida!** , clique na recomendação.

    [![selecionar correção rápida!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na guia **recursos não íntegros** , selecione os recursos em que você deseja implementar a recomendação e clique em **corrigir**.

    > [!NOTE]
    > Alguns dos recursos listados podem estar desabilitados, pois você não tem as permissões apropriadas para modificá-los.

1. Na caixa de confirmação, leia os detalhes e as implicações de correção.

    ![Correção rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações são listadas na caixa cinza na janela **corrigir recursos** que é aberta depois de clicar em **corrigir**. Eles listam as alterações que ocorrem ao prosseguir com a correção de correção rápida.

1. Insira os parâmetros relevantes, se necessário, e aprove a correção.

    > [!NOTE]
    > Pode levar vários minutos após a correção ser concluída para ver os recursos na guia **recursos íntegros** . Para exibir as ações de correção, verifique o [log de atividades](#activity-log).

1. Depois de concluído, uma notificação será exibida informando se a correção foi bem-sucedida.

## Log de correções de correção rápida no log de atividades<a name="activity-log"></a>

A operação de correção usa uma implantação de modelo ou chamada à API de PATCH REST para aplicar a configuração no recurso. Essas operações são registradas no [log de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Recomendações com a correção rápida de correção

|Recomendação|Implicação|
|---|---|
|A auditoria em servidores SQL deve ser habilitada|Esta ação habilitará a auditoria do SQL nesses servidores e em seus bancos de dados. <br>**Nota**: <ul><li>Para cada região dos SQL Servers selecionados, uma conta de armazenamento para salvar os logs de auditoria será criada e compartilhada por todos os servidores nessa região.</li><li>Para garantir a auditoria adequada, não exclua nem renomeie o grupo de recursos nem as contas de armazenamento.</li></ul>|
|A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas do SQL|Esta ação habilitará o ADS (segurança de dados avançada) do SQL nas instâncias gerenciadas do SQL selecionadas. <br>**Nota**: <ul><li>Para cada região e grupo de recursos das instâncias gerenciadas do SQL selecionadas, uma conta de armazenamento para salvar os resultados da verificação será criada e compartilhada por todas as instâncias nessa região.</li><li> Os anúncios são cobrados a $15 por instância gerenciada do SQL.</li></ul>|
|A avaliação de vulnerabilidade deve ser habilitada em suas instâncias gerenciadas do SQL|Esta ação habilitará a avaliação de vulnerabilidade do SQL nas instâncias gerenciadas do SQL selecionadas. <br>**Nota**:<ul><li>A avaliação de vulnerabilidade do SQL faz parte do pacote do SQL Advanced Data Security (ADS). Se o ADS ainda não estiver habilitado, ele será habilitado automaticamente na instância gerenciada.</li><li>Para cada região e grupo de recursos das instâncias gerenciadas do SQL selecionadas, uma conta de armazenamento para armazenar os resultados da verificação será criada e compartilhada por todas as instâncias nessa região.</li><li>Os anúncios são cobrados a $15 por SQL Server.</li></ul>||
|A segurança de dados avançada deve ser habilitada em seus servidores SQL|Esta ação habilitará o ADS (segurança de dados avançada) nesses servidores selecionados e seus bancos de dado. <br>**Nota**:<ul><li>Para cada região e grupo de recursos dos SQL Servers selecionados, uma conta de armazenamento para armazenar os resultados da verificação será criada e compartilhada por todos os servidores nessa região. <</li><li>Os anúncios são cobrados a $15 por SQL Server.</li></ul>||
|A avaliação de vulnerabilidade deve ser habilitada em seus servidores SQL|Esta ação habilitará a avaliação de vulnerabilidade do SQL nesses servidores selecionados e seus bancos de dados. <br>**Nota**:<ul><li>A avaliação de vulnerabilidade do SQL faz parte do pacote do SQL Advanced Data Security (ADS). Se o ADS ainda não estiver habilitado, ele será habilitado automaticamente no SQL Server.</li><li>Para cada região e grupo de recursos dos SQL Servers selecionados, uma conta de armazenamento para armazenar os resultados da verificação será criada e compartilhada por todas as instâncias nessa região.</li><li>Os anúncios são cobrados a $15 por SQL Server.</li></ul>||
|A Transparent Data Encryption em bancos de dados SQL deve ser habilitada|Essa ação habilita o TDE (SQL Database Transparent Data Encryption) nos bancos de dados selecionados. <br>**Observação**: por padrão, as chaves TDE gerenciadas pelo serviço serão usadas.
|A transferência segura para contas de armazenamento deve ser ativada|Essa ação atualiza a segurança da conta de armazenamento para permitir somente solicitações por conexões seguras. (HTTPS). <br>**Nota**:<ul><li>Todas as solicitações que usam HTTP serão rejeitadas.</li><li>Quando você estiver usando o serviço arquivos do Azure, a conexão sem criptografia falhará, incluindo cenários usando SMB 2,1, SMB 3,0 sem criptografia e alguns tipos do cliente Linux SMB. Saiba mais.</li></ul>|
|Aplicação Web só deve estar acessível através de HTTPS|Essa ação redirecionará todo o tráfego de HTTP para HTTPS nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto de extremidade HTTPS que não tenha um certificado SSL será exibido no navegador com um ' erro de privacidade '. Portanto, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Verifique se os firewalls do aplicativo Web e do pacote estão protegendo o serviço de aplicativo, permitir encaminhamento de sessões HTTPS.</li></ul>|
|Função de aplicação só deve estar acessível através de HTTPS|Essa ação redirecionará todo o tráfego de HTTP para HTTPS nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto de extremidade HTTPS que não tenha um certificado SSL será exibido no navegador com um ' erro de privacidade '. Portanto, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Verifique se os firewalls do aplicativo Web e do pacote estão protegendo o serviço de aplicativo, permitir encaminhamento de sessões HTTPS.</li></ul>|
|O aplicativo de API só deve ser acessível via HTTPS|Essa ação redirecionará todo o tráfego de HTTP para HTTPS nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto de extremidade HTTPS que não tenha um certificado SSL será exibido no navegador com um ' erro de privacidade '. Portanto, os usuários que têm um domínio personalizado precisam verificar se configuraram um certificado SSL.</li><li>Verifique se os firewalls do aplicativo Web e do pacote estão protegendo o serviço de aplicativo, permitir encaminhamento de sessões HTTPS.</li></ul>|
|Depuração remota deve ser desativada para a aplicação Web|Esta ação desabilita a depuração remota.|
|A depuração remota deve ser desativada para Aplicativo de funções|Esta ação desabilita a depuração remota.|
|A depuração remota deve ser desativada para o aplicativo de API|Esta ação desabilita a depuração remota.|
|O CORS não deve permitir que todos os recursos acessem seu aplicativo Web|Essa ação impede que outros domínios acessem seu aplicativo Web. Para permitir domínios específicos, insira-os no campo origens permitidas (separadas por vírgulas). <br>**Observação**: deixar o campo vazio bloqueará todas as chamadas entre origens. "título do campo de parâmetro:" origens permitidas "|
|CORS não deve permitir que todos os recursos de aceder à sua aplicação de função|Essa ação impede que outros domínios acessem seu aplicativo de funções. Para permitir domínios específicos, insira-os no campo origens permitidas (separadas por vírgulas). <br>**Observação**: deixar o campo vazio bloqueará todas as chamadas entre origens. "título do campo de parâmetro:" origens permitidas "|
|O CORS não deve permitir que todos os recursos acessem seu aplicativo de API|Essa ação impede que outros domínios acessem seu aplicativo de API. Para permitir domínios específicos, insira-os no campo origens permitidas (separadas por vírgulas). <br>**Observação**: deixar o campo vazio bloqueará todas as chamadas entre origens. "título do campo de parâmetro:" origens permitidas "|
|O agente de monitoramento deve ser habilitado em suas máquinas virtuais|Esta ação instala um agente de monitoramento nas máquinas virtuais selecionadas. Selecione um espaço de trabalho para o qual o agente se reportará.<ul><li>Se a política de atualização for definida como automática, ela será implantada em novas instâncias existentes.</li><li>Se a política de atualização estiver definida como manual e você quiser instalar o agente em instâncias existentes, selecione a opção caixa de seleção. [Saiba mais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Os logs de diagnóstico no Key Vault devem ser habilitados|Esta ação habilita os logs de diagnóstico em cofres de chaves. Os logs de diagnóstico e as métricas são salvos no espaço de trabalho selecionado.|
|Os logs de diagnóstico no barramento de serviço devem ser habilitados|Essa ação habilita os logs de diagnóstico no barramento de serviço. Os logs de diagnóstico e as métricas são salvos no espaço de trabalho selecionado.|

## <a name="next-steps"></a>Passos seguintes

Neste documento, você mostrou como corrigir recomendações na central de segurança. Para saber mais sobre a central de segurança, consulte os seguintes tópicos:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md) -saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) -saiba como monitorar a integridade dos recursos do Azure.
