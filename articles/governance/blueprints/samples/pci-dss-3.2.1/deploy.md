---
title: Exemplo-PCI-DSS v 3.2.1 Blueprint-etapas de implantação
description: Implante as etapas do exemplo de planejamento de plano de segurança de dados do setor de pagamento de Data Security Standard v 3.2.1.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: f95f9a592085fd93fba5e6b11a1a75609eb70295
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980930"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Implantar a amostra do PCI-DSS v 3.2.1 Blueprint

Para implantar a amostra do plano gráfico do Azure Blueprints PCI-DSS v 3.2.1, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre o exemplo de plano gráfico **PCI-DSS v 3.2.1** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: Forneça um nome para sua cópia do exemplo de plano gráfico PCI-DSS v 3.2.1.
   - **Local da definição**: Use as reticências e selecione o grupo de gerenciamento para salvar sua cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **Next: Artefatos @ no__t-0 na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e precisa, mas essa modificação pode movê-lo para fora do padrão PCI-DSS v 3.2.1.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada do exemplo de plano gráfico PCI-DSS v 3.2.1". Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - Noções Básicas

     - **Subscrições**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada uma usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere conforme necessário ou deixe como está.
     - **Local**: Selecione uma região na qual a identidade gerenciada deve ser criada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: Escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Atribuição de Bloqueio

     Selecione a configuração de bloqueio Blueprint para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerenciada _atribuída pelo sistema_ padrão.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Preview @ no__t-1 Audit PCI v 3.2.1: controles de 2018 e implante extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de Política|Lista de tipos de recursos | Configuração de diagnóstico de auditoria para tipos de recursos selecionados. O valor padrão é que todos os recursos estão selecionados| 
|Localizações permitidas|Atribuição de Política|Lista de locais permitidos|Lista de locais de data center permitidos para qualquer recurso a ser implantado. Essa lista é personalizável para os locais do Azure desejados globalmente. Selecione os locais que você deseja permitir.| 
|Locais permitidos para grupos de recursos|Atribuição de Política |Local permitido |Essa política permite restringir os locais em que sua organização pode criar grupos de recursos. Use o para impor seus requisitos de conformidade geográfica.| 
|Implementar a Auditoria em servidores SQL|Atribuição de Política|Dias de retenção|A retenção de dados em número de dias. O valor padrão é 180, mas o PCI requer 365.| 
|Implementar a Auditoria em servidores SQL|Atribuição de Política|Nome do grupo de recursos para a conta de armazenamento|A auditoria grava eventos de banco de dados em um log de auditoria na sua conta de armazenamento do Azure (uma conta de armazenamento será criada em cada região em que um SQL Server é criado e será compartilhado por todos os servidores nessa região).| 

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou as etapas para implantar o exemplo de plano gráfico PCI-DSS v 3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 Blueprint-visão geral](./index.md)
> [PCI-DSS v 3.2.1 Blueprint – mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
