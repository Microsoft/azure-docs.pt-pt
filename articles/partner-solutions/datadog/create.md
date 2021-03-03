---
title: Criar soluções parceiras da Datadog - Azure
description: Este artigo descreve como usar o portal Azure para criar uma instância de Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748894"
---
# <a name="quickstart-get-started-with-datadog"></a>QuickStart: Começa com o Datadog

Neste arranque rápido, criará um exemplo de Datadog. Pode criar uma nova organização Datadog ou ligar-se a uma organização de Datadog existente.

## <a name="pre-requisites"></a>Pré-requisitos

Para configurar a integração do Azure Datadog, tem de ter acesso **ao Proprietário** na subscrição do Azure. Certifique-se de que tem o acesso adequado antes de iniciar a configuração.

Para utilizar a funcionalidade De Sign-On Única (SSO) de afirmação de segurança no recurso Datadog, tem de configurar uma aplicação empresarial. Para adicionar uma aplicação de empresa, precisa de uma destas funções: Administrador Global, Administrador de Aplicação cloud, Administrador de Aplicação ou proprietário do principal de serviço.

Utilize os seguintes passos para configurar a aplicação da empresa:

1. Ir para o [portal Azure.](https://portal.azure.com) Selecione **Azure Active Directory**.
1. No painel esquerdo, selecione **Aplicações empresariais**.
1. Selecione **Nova Aplicação**.
1. In **Add from the gallery**, search for *Datadog*. Selecione o resultado da pesquisa e, em seguida, **selecione Adicionar**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Aplicação Datadog na galeria de empresas AAD." border="true":::

1. Assim que a aplicação for criada, vá para as propriedades a partir do painel lateral. Definir **a atribuição do utilizador necessária para** **Não,** e selecione **Guardar**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Definir propriedades para a aplicação Datadog" border="true":::

1. Vá para **um único sinal** do painel lateral. Em seguida, selecione **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="Autenticação SAML." border="true":::

1. Selecione **Sim** quando solicitado para **Guardar as definições de sposição única .**

   :::image type="content" source="media/create/save-sso.png" alt-text="Guarde um único sinal para a aplicação Datadog" border="true":::

1. A configuração de um único sinal está agora completa.

## <a name="find-offer"></a>Encontre oferta

Use o portal Azure para encontrar Datadog.

1. Aceda ao [Portal do Azure](https://portal.azure.com/) e inicie sessão.

1. Se visitou o **Marketplace** numa sessão recente, selecione o ícone das opções disponíveis. Caso contrário, procure _no Marketplace._

    :::image type="content" source="media/create/marketplace.png" alt-text="Ícone do mercado.":::

1. No Marketplace, procure **por Datadog.**

1. No ecrã geral do plano, selecione **Configurar + subscrever**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Aplicação Datadog no Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Criar um recurso Datadog em Azure

O portal apresenta um formulário para criar o recurso Datadog.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Criar recurso Datadog" border="true":::

Fornecer os seguintes valores.

|Propriedade | Descrição
|:-----------|:-------- |
| Subscrição | Selecione a subscrição Azure que pretende utilizar para criar o recurso Datadog. Deve ter acesso ao dono. |
| Grupo de recursos | Especifique se quer criar um novo grupo de recursos ou utilizar um existente. Um [grupo de recursos](../../azure-resource-manager/management/overview.md#resource-groups) é um contentor que mantém recursos relacionados para uma solução do Azure. |
| Nome do recurso | Especifique um nome para o recurso Datadog. Este nome será o nome da nova organização Datadog, ao criar uma nova organização datadog. |
| Localização | Selecione E.U.A. Oeste 2. Atualmente, o West US 2 é a única região apoiada. |
| Organização Datadog | Para criar uma nova organização datadog, selecione **New**. Para ligar a uma organização de Datadog existente, selecione **Existing**. |
| Plano de preços | Ao criar uma nova organização, selecione a partir da lista de planos disponíveis do Datadog. |
| Prazo de faturação | Mensalmente. |

Se estiver a ligar-se a uma organização de Datadog existente, consulte a secção seguinte. Caso contrário, selecione **Seguinte: Métricas e troncos** e salte a secção seguinte.

## <a name="link-to-existing-datadog-organization"></a>Link para a organização datadog existente

Pode ligar o seu novo recurso Datadog em Azure a uma organização de Datadog existente.

Selecione **Existing** for Data organization e, em seguida, **selecione Link to Datadog org**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Link para a organização datadog existente." border="true":::

O link abre uma janela de autenticação datadog. Inscreva-se no Datadog.

Por predefinição, o Azure liga a sua atual organização Datadog ao seu recurso Datadog. Se quiser ligar-se a uma organização diferente, selecione a organização apropriada na janela de autenticação, como mostrado abaixo.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Selecione a organização adequada do Datadog para ligar" border="true":::

## <a name="configure-metrics-and-logs"></a>Configure métricas e troncos

Utilize etiquetas de recursos Azure para configurar quais métricas e registos são enviados para Datadog. Pode incluir ou excluir métricas e registos para recursos específicos.

As regras de etiqueta para o envio **de métricas** são:

- Por padrão, são recolhidas métricas para todos os recursos, exceto máquinas virtuais, conjuntos de escala de máquinas virtuais e planos de serviço de aplicações.
- Máquinas virtuais, conjuntos de escala de máquinas virtuais e planos de serviço de aplicações com etiquetas *incluem* métricas para Datadog.
- Máquinas virtuais, conjuntos de escala de máquinas virtuais e planos de serviço de aplicações com tags *Exclude* não enviam métricas para Datadog.
- Se há um conflito entre as regras de inclusão e exclusão, a exclusão tem prioridade

As regras de etiquetas para envio **de registos** são:

- Por padrão, os registos são recolhidos para todos os recursos.
- Recursos Azure com *Incluir* tags enviar registos para Datadog.
- Os recursos Azure com tags  *Excluidores* não enviam registos para o Datadog.
- Se há um conflito entre as regras de inclusão e exclusão, a exclusão tem prioridade.

Por exemplo, a imagem abaixo mostra uma regra de tag onde apenas as máquinas virtuais, conjuntos de escala de máquinas virtuais e planos de serviço de aplicações marcados como *Datadog = Verdadeiro* envio métricas para Datadog.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Configurar registos e métricas." border="true":::

Existem dois tipos de registos que podem ser emitidos de Azure a Datadog.

1. **Registos de nível de subscrição** - Forneça informações sobre as operações dos seus recursos no [plano de controlo](../../azure-resource-manager/management/control-plane-and-data-plane.md). Estão também incluídas atualizações sobre os eventos de saúde do serviço. Utilize o registo de atividade para determinar o quê, quem e quando para quaisquer operações de escrita (PUT, POST, DELETE). Há um único registo de atividade para cada subscrição do Azure.

1. **Registos de recursos Azure** - Forneça informações sobre as operações que foram tomadas num recurso Azure no [plano de dados](../../azure-resource-manager/management/control-plane-and-data-plane.md). Por exemplo, obter um segredo de um Cofre chave é uma operação de avião de dados. Ou, fazer um pedido a uma base de dados também é uma operação de data plane. O conteúdo dos registos de recursos varia consouros e de tipo de recurso.

Para enviar registos de nível de subscrição para Datadog, selecione **Enviar registos de atividade de subscrição**. Se esta opção não for verificada, nenhum dos registos de nível de subscrição é enviado para o Datadog.

Para enviar registos de recursos Azure para Datadog, selecione **Enviar registos de recursos Azure para todos os recursos definidos**. Os tipos de registos de recursos Azure estão listados nas [categorias de Registo de Recursos do Monitor Azure](../../azure-monitor/essentials/resource-logs-categories.md).  Para filtrar o conjunto de recursos Azure que enviam registos para o Datadog, utilize etiquetas de recursos Azure.  

Os registos enviados para datadog serão cobrados pela Azure. Para mais informações, consulte os [preços dos registos da plataforma enviados](https://azure.microsoft.com/pricing/details/monitor/) aos parceiros do Azure Marketplace.

Uma vez concluída a configuração de métricas e registos, selecione **Seguinte: Único sinal de início**.

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único

Se a sua organização utilizar o Azure Ative Directory como seu fornecedor de identidade, pode estabelecer um único sinal de acesso do portal Azure para o Datadog. Se a sua organização utilizar um fornecedor de identidade diferente ou não quiser estabelecer um único sinal de saúde neste momento, pode saltar esta secção.

Se estiver a ligar o recurso Datadog a uma organização de Datadog existente, não pode configurar um único sinal neste passo. Em vez disso, configura um único s-on depois de criar o recurso Datadog. Para obter mais informações, consulte [reconfigurar o único sinal de sação](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Único sinal de ligação à organização datadog existente." border="true":::

Para estabelecer um único sinal de entrada através do diretório Azure Ative, selecione a caixa de verificação para **ativar um único sinal através do Azure Ative Directory**.

O portal Azure recupera a aplicação datadog apropriada do Azure Ative Directory. A aplicação corresponde à aplicação Enterprise que forneceu num passo anterior.

Selecione o nome da aplicação Datadog.

:::image type="content" source="media/create/sso.png" alt-text="Ativar o único sinal de 'on' para o Datadog." border="true":::

Selecione **Seguinte: Tags**.

## <a name="add-custom-tags"></a>Adicionar tags personalizadas

Pode especificar etiquetas personalizadas para o novo recurso Datadog. Forneça pares de nomes e valores para que as tags se apliquem ao recurso Datadog.

:::image type="content" source="media/create/tags.png" alt-text="Adicione etiquetas personalizadas para o recurso Datadog." border="true":::

Quando terminar de adicionar tags, selecione **Seguinte: Review+Create**.

## <a name="review--create-datadog-resource"></a>Comentário + Criar recurso Datadog

Reveja as suas seleções e os termos de utilização. Após a validação concluída, **selecione Criar**.

:::image type="content" source="media/create/review-create.png" alt-text="Reveja e crie recurso Datadog." border="true":::

O Azure implementa o recurso Datadog.

Quando o processo estiver concluído, selecione **Ir ao Recurso** para ver o recurso Datadog.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Implementação de recursos datadog." border="true":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o recurso Datadog](manage.md)
