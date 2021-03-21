---
title: Resolução de problemas para soluções de parceiros datadog - Azure
description: Este artigo fornece informações sobre a resolução de problemas para datadog on Azure.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 0e3c82f711de4cd9710c9aafe798a986e3403ed4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563713"
---
# <a name="troubleshooting-datadog-on-azure"></a>Resolver problemas do Datadog no Azure

Este documento contém informações sobre a resolução de problemas das suas soluções que utilizam o Datadog.

## <a name="purchase-errors"></a>Erros de compra

* A compra falha porque um cartão de crédito válido não está ligado à subscrição do Azure ou um método de pagamento não está associado à subscrição.

  Utilize uma subscrição Azure diferente. Ou, adicione ou atualize o cartão de crédito ou o método de pagamento para a subscrição. Para obter mais informações, consulte [a atualização do método de crédito e pagamento.](../../cost-management-billing/manage/change-credit-card.md)

* A subscrição da EA não permite compras no Marketplace.

  Use uma subscrição diferente. Ou, verifique se a subscrição da EA está ativada para a compra do Marketplace. Para mais informações, consulte [as compras do Enable Marketplace](../../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases). Se essas opções não resolverem o problema, contacte o [suporte do Datadog](https://www.datadoghq.com/support).

## <a name="unable-to-create-datadog-resource"></a>Incapaz de criar recurso Datadog

Para configurar a integração do Azure Datadog, tem de ter acesso **ao Proprietário** na subscrição do Azure. Certifique-se de que tem o acesso adequado antes de iniciar a configuração.

## <a name="single-sign-on-errors"></a>Erros de inscrição únicos

**Incapaz de guardar as definições de inscrição única** - Este erro acontece onde há outra aplicação da Enterprise que está a usar o identificador SAML datadog. Para descobrir qual a aplicação que a está a utilizar, **selecione Editar** na secção De Configuração Básica SAML.

Para resolver este problema, desativar a outra app ou usar a outra app como a app Enterprise para configurar o SAML SSO com o Datadog. Se decidir utilizar a outra aplicação, certifique-se de que a aplicação tem as [definições necessárias.](create.md#configure-single-sign-on)

**App não mostra na página de definição de início de sposição única** - Primeiro, procure o ID da aplicação. Se não for mostrado nenhum resultado, verifique as definições DE SAML da aplicação. A grelha só mostra aplicações com definições SAML corretas. 

A URL do identificador deve ser `https://us3.datadoghq.com/account/saml/metadata.xml` .

A URL de resposta deve ser `https://us3.datadoghq.com/account/saml/assertion` .

A imagem a seguir mostra os valores corretos.
  
:::image type="content" source="media/troubleshoot/troubleshooting.png" alt-text="Verifique as definições de SAML para a aplicação Datadog em AAD." border="true":::

**Os utilizadores convidados convidados para o inquilino não conseguem aceder a um único sinal** - Alguns utilizadores têm dois endereços de e-mail no portal Azure. Normalmente, um e-mail é o nome principal do utilizador (UPN) e o outro e-mail é um e-mail alternativo.

Ao convidar o utilizador convidado, utilize o inquilino da casa UPN. Ao utilizar a UPN, mantém o endereço de e-mail sincronizado durante o processo de inscrição única. Pode encontrar a UPN procurando o endereço de e-mail no canto superior direito do portal Azure do utilizador.
  
## <a name="logs-not-being-emitted"></a>Registos não sendo emitidos

Apenas os recursos listados nas categorias de registo de recursos do Azure Monitor emitem registos para o Datadog. Para verificar se o recurso está a emitir registos para o Datadog, navegue para a definição de diagnóstico do Azure para o recurso específico. Verifique se há uma definição de diagnóstico do Datadog.

:::image type="content" source="media/troubleshoot/diagnostic-setting.png" alt-text="Definição de diagnóstico de Datadog no recurso Azure" border="true":::

## <a name="metrics-not-being-emitted"></a>Métricas não emitidas

O recurso Datadog é atribuído a uma **função de Leitor de Monitorização** na subscrição apropriada do Azure. Esta função permite ao recurso Datadog recolher métricas e enviar essas métricas para o Datadog.

Para verificar se o recurso tem a atribuição correta de função, abra o portal Azure e selecione a subscrição. No painel esquerdo, selecione **Access Control (IAM)**. Procure o nome do recurso Datadog. Confirme que o recurso Datadog tem a atribuição de função **de Leitor de Monitorização,** como mostrado abaixo.

:::image type="content" source="media/troubleshoot/datadog-role-assignment.png" alt-text="Atribuição de funções datadog na subscrição do Azure" border="true":::

## <a name="datadog-agent-installation-fails"></a>Instalação do agente Datadog falha

A integração Azure Datadog proporciona-lhe a capacidade de instalar o agente Datadog numa máquina virtual ou serviço de aplicações. Para configurar o agente Datadog, é utilizada a chave API selecionada como **Chave Padrão** no ecrã de Chaves API. Se uma chave predefinida não for selecionada, a instalação do agente Datadog falhará.

Se o agente Datadog tiver sido configurado com uma chave incorreta, navegue no ecrã das teclas API e altere a **chave predefinida**. Terá de desinstalar o agente Datadog e reinstalá-lo para configurar a máquina virtual com as novas teclas API.

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre como gerir o seu exemplo](manage.md) de Datadog.
