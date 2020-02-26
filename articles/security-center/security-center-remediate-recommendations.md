---
title: Remediar recomendações no Centro de Segurança Do Azure Microsoft Docs
description: Este artigo explica como remediar recomendações no Centro de Segurança Azure para proteger os seus recursos e cumprir as políticas de segurança.
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
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603495"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Remediar recomendações no Centro de Segurança Azure

As recomendações dão-lhe sugestões sobre como proteger melhor os seus recursos. Implementa uma recomendação seguindo as medidas de reparação previstas na recomendação.

## Passos de reparação<a name="remediation-steps"></a>

Depois de rever todas as recomendações, decida qual remediar primeiro. Recomendamos que utilize o [impacto Secure Score](security-center-recommendations.md#monitor-recommendations) para ajudar a priorizar o que fazer primeiro.

1. Na lista, clique na recomendação.

1. Siga as instruções na secção de passos de **reparação.** Cada recomendação tem o seu próprio conjunto de instruções. A imagem seguinte mostra passos de reparação para configurar aplicações para permitir apenas tráfego sobre HTTPS.

    ![Detalhes da recomendação](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Uma vez concluída, aparece uma notificação informando-o se a reparação tiver sido bem sucedida.

## Reparação de Correção Rápida<a name="one-click"></a>

A Quick Fix permite-lhe remediar rapidamente uma recomendação sobre vários recursos. Só está disponível para recomendações específicas. A Quick Fix simplifica a reparação e permite-lhe aumentar rapidamente a sua Pontuação Segura, melhorando a segurança do seu ambiente.

Para implementar a reparação quick fix:

1. Da lista de recomendações que têm a **Correção Rápida!** etiqueta, clique na recomendação.

    [![Selecione Quick Fix!](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A partir do separador **de recursos não saudáveis,** selecione os recursos em que pretende implementar a recomendação e clique em **Remediar**.

    > [!NOTE]
    > Alguns dos recursos listados podem ser desativados, porque não tem as permissões adequadas para modificá-los.

1. Na caixa de confirmação, leia os detalhes e implicações da reparação.

    ![Correção Rápida](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > As implicações estão listadas na caixa cinzenta na janela **de recursos Remediatados** que se abre após clicar em **Remediar**. Eles listam as mudanças que acontecem quando prosseguem com a reparação quick fix.

1. Insira os parâmetros relevantes, se necessário, e aprove a reparação.

    > [!NOTE]
    > Pode levar vários minutos após a reparação completa para ver os recursos no separador **de recursos Saudáveis.** Para visualizar as ações de reparação, verifique o registo de [atividade](#activity-log).

1. Uma vez concluída, aparece uma notificação informando-o se a reparação tiver sido bem sucedida.

## Registo de reparação de correção rápida no registo de atividade<a name="activity-log"></a>

A operação de reparação utiliza uma utilização do modelo ou uma chamada DERQUÍmea REST PATCH para aplicar a configuração no recurso. Estas operações estão registadas no registo de [atividade sinuosa do Azure.](../azure-resource-manager/management/view-activity-logs.md)


## <a name="recommendations-with-quick-fix-remediation"></a>Recomendações com reparação de correção rápida

|Recomendação|Implicação|
|---|---|
|A auditoria aos servidores SQL deve ser ativada|Esta ação permitirá a auditoria da SQL nestes servidores e nas suas bases de dados. <br>**Nota**: <ul><li>Para cada região dos servidores SQL selecionados, será criada e partilhada uma conta de armazenamento para guardar registos de auditoria e partilhada por todos os servidores daquela região.</li><li>Para garantir uma auditoria adequada, não elimine ou mude o nome do grupo de recursos ou das contas de armazenamento.</li></ul>|
|A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL|Esta ação permitirá à SQL Advanced Data Security (ADS) nas instâncias geridas pela SQL selecionadas. <br>**Nota**: <ul><li>Para cada região e grupo de recursos dos casos geridos pela SQL selecionados, será criada e partilhada uma conta de armazenamento para os resultados da poupança e partilhada por todos os casos daquela região.</li><li> O ADS é cobrado a $15 por instância gerida pela SQL.</li></ul>|
|A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL|Esta ação permitirá a Avaliação de Vulnerabilidade SQL nas instâncias geridas pelo SQL selecionadas. <br>**Nota**:<ul><li>A Avaliação de Vulnerabilidade SQL faz parte do pacote SQL Advanced Data Security (ADS). Se o ADS ainda não estiver ativado, será ativado automaticamente na instância gerida.</li><li>Para cada região e grupo de recursos dos casos geridos pela SQL selecionados, será criada e partilhada uma conta de armazenamento para armazenamento de resultados de digitalização e partilhada por todos os casos daquela região.</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A Segurança avançada de Dados deve ser ativada nos seus servidores SQL|Esta ação permitirá a Segurança avançada de Dados (ADS) nestes servidores selecionados e nas suas bases de dados. <br>**Nota**:<ul><li>Para cada região e grupo de recursos dos servidores SQL selecionados, será criada e partilhada uma conta de armazenamento para armazenamento de resultados de digitalização e partilhada por todos os servidores daquela região.<</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A Avaliação de Vulnerabilidade deve ser ativada nos seus servidores SQL|Esta ação permitirá a Avaliação de Vulnerabilidade SQL nestes servidores selecionados e nas suas bases de dados. <br>**Nota**:<ul><li>A Avaliação de Vulnerabilidade SQL faz parte do pacote SQL Advanced Data Security (ADS). Se o ADS ainda não estiver ativado, será ativado automaticamente no servidor SQL.</li><li>Para cada região e grupo de recursos dos servidores SQL selecionados, será criada e partilhada uma conta de armazenamento para armazenamento de resultados de digitalização e partilhada por todas as instâncias daquela região.</li><li>O ADS é cobrado a $15 por servidor SQL.</li></ul>||
|A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Esta ação permite a Encriptação transparente de dados transparentes (TDE) da Base de Dados SQL nas bases de dados selecionadas. <br>**Nota:** Por defeito, serão utilizadas chaves TDE geridas pelo serviço.
|Transferência segura para contas de armazenamento deve ser ativada|Esta ação atualiza a segurança da sua conta de armazenamento para permitir apenas pedidos por ligações seguras. (HTTPS). <br>**Nota**:<ul><li>Quaisquer pedidos que utilizem HTTP serão rejeitados.</li><li>Quando estiver a utilizar o serviço de ficheiros Azure, a ligação sem encriptação falhará, incluindo cenários que utilizem SMB 2.1, SMB 3.0 sem encriptação e alguns sabores do cliente Linux SMB. Saiba mais.</li></ul>|
|Aplicação Web só deve estar acessível através de HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que possuem um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web que protegem o serviço de aplicações, permitem o reencaminhamento de sessões HTTPS.</li></ul>|
|Função de aplicação só deve estar acessível através de HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que possuem um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web que protegem o serviço de aplicações, permitem o reencaminhamento de sessões HTTPS.</li></ul>|
|A Aplicação API só deve ser acessível em HTTPS|Esta ação redirecionará todo o tráfego de HTTP para HTTPS, nos recursos selecionados. <br>**Nota**:<ul><li>Um ponto final HTTPS que não tenha um certificado SSL aparecerá no navegador com um 'Erro de Privacidade'. Assim, os utilizadores que possuem um domínio personalizado precisam de verificar se criaram um certificado SSL.</li><li>Certifique-se de que as firewalls de pacotes e aplicações web que protegem o serviço de aplicações, permitem o reencaminhamento de sessões HTTPS.</li></ul>|
|Depuração remota deve ser desativada para a aplicação Web|Esta ação desativa a depuração remota.|
|Depuração remota deve ser desligada para app de função|Esta ação desativa a depuração remota.|
|Depuração remota deve ser desligada para App API|Esta ação desativa a depuração remota.|
|O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web|Esta ação impede outros domínios de aceder à sua Aplicação Web. Para permitir domínios específicos, introduza-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: "Origens permitidas"|
|CORS não deve permitir que todos os recursos de aceder à sua aplicação de função|Esta ação impede outros domínios de aceder à sua Aplicação de Função. Para permitir domínios específicos, introduza-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: "Origens permitidas"|
|O CORS não deve permitir que todos os recursos acedam à sua App API|Esta ação impede outros domínios de aceder à sua Aplicação API. Para permitir domínios específicos, introduza-os no campo de origens permitidas (separados por vírgulas). <br>**Nota:** Deixar o campo vazio bloqueará todas as chamadas de origem cruzada.'Título de campo param: "Origens permitidas"|
|O agente de monitorização deve ser ativado nas suas máquinas virtuais|Esta ação instala um agente de monitorização nas máquinas virtuais selecionadas. Selecione um espaço de trabalho para o agente se apresentar.<ul><li>Se a sua política de atualização estiver definida como automática, irá implementar-se em novas instâncias existentes.</li><li>Se a sua política de atualização estiver definida manualmente e quiser instalar o agente em instâncias existentes, selecione a opção caixa de verificação. [Saiba mais](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Os registos de diagnóstico no Cofre-Chave devem ser ativados|Esta ação permite registos de diagnóstico em cofres chave. Os registos e métricas de diagnóstico são guardados no espaço de trabalho selecionado.|
|Os registos de diagnóstico no autocarro de serviço devem ser ativados|Esta ação permite registos de diagnóstico no autocarro de serviço. Os registos e métricas de diagnóstico são guardados no espaço de trabalho selecionado.|

## <a name="next-steps"></a>Passos seguintes

Neste documento, foi-lhe mostrado como remediar as recomendações no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte os seguintes tópicos:

* [Definição de políticas](tutorial-security-policy.md) de segurança no Azure Security Center - Saiba como configurar as políticas de segurança para as suas subscrições e grupos de recursos Do Azure.
* [Monitorização da saúde de segurança no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
