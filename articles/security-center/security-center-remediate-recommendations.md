---
title: Remediar recomendações no Centro de Segurança Azure Microsoft Docs
description: Este artigo explica como remediar recomendações no Azure Security Center para proteger os seus recursos e cumprir as políticas de segurança.
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
ms.openlocfilehash: 8f191fffd1c8d66ac959953c55cbc79b3807c803
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204557"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Recomendações de remediação no Centro de Segurança do Azure

As recomendações dão-lhe sugestões sobre como garantir melhor os seus recursos. Implementa uma recomendação seguindo as medidas de reparação previstas na recomendação.

## <a name="remediation-steps"></a>Medidas de reparação<a name="remediation-steps"></a>

Depois de rever todas as recomendações, decida qual delas deve remediar primeiro. Recomendamos que utilize o [impacto Secure Score](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na secção **de passos de reparação.** Cada recomendação tem o seu próprio conjunto de instruções. A imagem que se segue mostra medidas de reparação para configurar aplicações para permitir apenas o tráfego em HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Uma vez concluída, aparece uma notificação informando-o se a reparação foi bem sucedida.

## <a name="quick-fix-remediation"></a>Reparação rápida<a name="one-click"></a>

O Quick Fix permite-lhe remediar rapidamente uma recomendação sobre múltiplos recursos. Só está disponível para recomendações específicas. O Quick Fix simplifica a remediação e permite-lhe aumentar rapidamente a sua Pontuação Segura, melhorando a segurança do seu ambiente.

Para implementar a reparação da Quick Fix:

1. Da lista de recomendações que têm a **Quick Fix!** etiqueta, clique na recomendação.

    [![Selecione Quick Fix!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A partir do separador **recursos insalubres,** selecione os recursos em que pretende implementar a recomendação e clique em **Remediate**.

    > [!NOTE]
    > Alguns dos recursos listados podem ser desativados, porque não tem as permissões apropriadas para os modificar.

1. Na caixa de confirmação, leia os detalhes e implicações da reparação.

    ![Correção Rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações estão listadas na caixa cinzenta na janela **de recursos Remediate** que se abre após clicar em **Remediate**. Eles listam as mudanças que acontecem quando prosseguem com a reparação da Quick Fix.

1. Insira os parâmetros relevantes se necessário e aprove a reparação.

    > [!NOTE]
    > Pode levar vários minutos após a reparação concluir para ver os recursos no separador **recursos saudáveis.** Para visualizar as ações de reparação, verifique o registo de [atividades](#activity-log).

1. Uma vez concluída, aparece uma notificação informando-o se a reparação foi bem sucedida.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Reparação rápida registação no registo de atividade<a name="activity-log"></a>

A operação de remediação utiliza uma implementação de modelo ou chamada API REST PATCH para aplicar a configuração no recurso. Estas operações estão registadas no [registo de atividades do Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Recomendações com reparação de Quick Fix

|Recomendação|Implicação|
|---|---|
|A auditoria na Base de Dados SQL deve ser ativada|Esta ação permitirá a auditoria da SQL nestes servidores e nas suas bases de dados. <br>**Nota:** <ul><li>Para cada região da base de dados SQL selecionada, será criada e partilhada uma conta de armazenamento para guardar registos de auditoria por todos os servidores dessa região.</li><li>Para garantir uma auditoria adequada, não elimine ou mude o nome do grupo de recursos ou das contas de armazenamento.</li></ul>|
|A segurança avançada dos dados deve ser ativada em SqL Managed Instance|Esta ação permitirá a SQL Advanced Data Security (ADS) nas instâncias geridas sql selecionadas. <br>**Nota:** <ul><li>Para cada região e grupo de recursos da 222CL Managed Instance selecionada, será criada e partilhada uma conta de armazenamento para a poupança de resultados da verificação por todos os casos nessa região.</li><li> A ADS é cobrada a $15 por SQL Managed Instance.</li></ul>|
|A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL|Esta ação permitirá a avaliação da vulnerabilidade do SQL na ocorrência gerida pelo SQL selecionado. <br>**Nota:**<ul><li>A SqL Vulnerability Assessment faz parte do pacote SQL Advanced Data Security (ADS). Se o ADS ainda não estiver ativado, será automaticamente ativado na instância gerida.</li><li>Para cada região e grupo de recursos da 222CL Managed Instance selecionada, será criada e partilhada uma conta de armazenamento para armazenar resultados de digitalização por todas as instâncias daquela região.</li><li>A ADS é cobrada a $15 por Base de Dados SQL.</li></ul>||
|A Segurança Avançada de Dados deve ser ativada na sua Base de Dados SQL|Esta ação permitirá a Segurança Avançada de Dados (ADS) nestes servidores selecionados e nas suas bases de dados. <br>**Nota:**<ul><li>Para cada região e grupo de recursos da base de dados SQL selecionada, será criada e partilhada por todos os servidores dessa região.<</li><li>A ADS é cobrada a $15 por Base de Dados SQL.</li></ul>||
|A Avaliação de Vulnerabilidades deve ser ativada na sua Base de Dados SQL|Esta ação permitirá a Avaliação de Vulnerabilidades SQL nestes servidores selecionados e nas suas bases de dados. <br>**Nota:**<ul><li>A SqL Vulnerability Assessment faz parte do pacote SQL Advanced Data Security (ADS). Se o ADS ainda não estiver ativado, será automaticamente ativado na Base de Dados SQL.</li><li>Para cada região e grupo de recursos da base de dados SQL selecionada, será criada e partilhada uma conta de armazenamento para armazenar resultados de digitalização por todas as instâncias dessa região.</li><li>A ADS é cobrada a $15 por Base de Dados SQL.</li></ul>||
|A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Esta ação permite a encriptação de dados transparentes da base de dados SQL (TDE) nas bases de dados selecionadas. <br>**Nota:** Por predefinição, serão utilizadas teclas TDE geridas pelo serviço.
|A transferência segura para contas de armazenamento deve ser ativada|Esta ação atualiza a segurança da sua conta de armazenamento para permitir apenas pedidos através de ligações seguras. (HTTPS). <br>**Nota:**<ul><li>Quaisquer pedidos que utilizem HTTP serão rejeitados.</li><li>Quando estiver a utilizar o serviço de ficheiros Azure, a ligação sem encriptação falhará, incluindo cenários que utilizem SMB 2.1, SMB 3.0 sem encriptação e alguns sabores do cliente Linux SMB. Mais informações.</li></ul>|
|A Aplicação Web só deve ser acessível em HTTPS|Esta ação irá redirecionar todo o tráfego de HTTP para HTTPS, sobre os recursos selecionados. <br>**Nota:**<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que tenham um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web protegem o serviço de aplicações, permita o encaminhamento de sessões HTTPS.</li></ul>|
|A App de função só deve estar acessível através do HTTPS|Esta ação irá redirecionar todo o tráfego de HTTP para HTTPS, sobre os recursos selecionados. <br>**Nota:**<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que tenham um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web protegem o serviço de aplicações, permita o encaminhamento de sessões HTTPS.</li></ul>|
|A API App só deve estar acessível em HTTPS|Esta ação irá redirecionar todo o tráfego de HTTP para HTTPS, sobre os recursos selecionados. <br>**Nota:**<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que tenham um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web protegem o serviço de aplicações, permita o encaminhamento de sessões HTTPS.</li></ul>|
|Depuragem remota deve ser desligada para aplicação web|Esta ação desativa a depuração remota.|
|Depuragem remota deve ser desligada para a App de Função|Esta ação desativa a depuração remota.|
|Depuragem remota deve ser desligada para app API|Esta ação desativa a depuração remota.|
|O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web|Esta ação bloqueia outros domínios de aceder à sua Aplicação Web. Para permitir domínios específicos, insira-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O CORS não deve permitir que todos os recursos acedam à sua App de Função|Esta ação bloqueia outros domínios de aceder à sua Aplicação de Função. Para permitir domínios específicos, insira-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O CORS não deve permitir que todos os recursos acedam à sua App API|Esta ação impede que outros domínios acedam à sua Aplicação API. Para permitir domínios específicos, insira-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: 'Origens permitidas'|
|O agente de monitorização deve ser ativado nas suas máquinas virtuais|Esta ação instala um agente de monitorização nas máquinas virtuais selecionadas. Selecione um espaço de trabalho para o agente se apresentar.<ul><li>Se a sua política de atualização for definida como automática, irá implementar em novas instâncias existentes.</li><li>Se a sua política de atualização estiver definida como manual e pretender instalar o agente em instâncias existentes, selecione a opção caixa de verificação. [Saiba mais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Os registos de diagnóstico no Cofre de Chaves devem ser ativados|Esta ação permite registos de diagnóstico em cofres-chave. Registos e métricas de diagnóstico são guardados no espaço de trabalho selecionado.|
|Os registos de diagnóstico no autocarro de serviço devem ser ativados|Esta ação permite registos de diagnóstico no autocarro de serviço. Registos e métricas de diagnóstico são guardados no espaço de trabalho selecionado.|

## <a name="next-steps"></a>Passos seguintes

Neste documento, foi-lhe mostrado como remediar recomendações no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte os seguintes tópicos:

* [Definir políticas de segurança no Azure Security Center](tutorial-security-policy.md) - Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos Azure.
* [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
