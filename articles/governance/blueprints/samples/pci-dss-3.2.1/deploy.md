---
title: Exemplo - esquema de v3.2.1 PCI-DSS - implementar passos
description: Implemente os passos do exemplo de plano gráfico v3.2.1 pagamento Card Industry Data Security Standard.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561373"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Implementar o exemplo de plano gráfico de v3.2.1 PCI-DSS

Para implementar o exemplo de esquema do Azure esquemas PCI-DSS v3.2.1, é necessário os seguintes passos:

> [!div class="checklist"]
> - Criar um novo plano de gráfico do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Em primeiro lugar, implementa o exemplo de esquema ao criar uma novo esquema no seu ambiente com o exemplo de como um starter.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **v3.2.1 PCI-DSS** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de plano gráfico de v3.2.1 PCI-DSS.
   - **Localização da definição**: Utilize o botão de reticências e selecione o grupo de gestão para guardar a sua cópia do exemplo para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que constituem o exemplo de esquema. Muitos dos artefatos os parâmetros que vamos definir mais tarde. Selecione **Guardar rascunho** quando terminar a rever o exemplo de esquema.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada. A cópia do exemplo de esquema pode ser personalizada para seu ambiente e as necessidades, mas que a modificação pode movê-lo na direção oposta a v3.2.1 PCI-DSS padrão.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. A nova página à direita, fornecem uma **versão** para obter uma cópia do exemplo de esquema. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicada do exemplo de plano gráfico de v3.2.1 PCI-DSS." Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois da cópia do exemplo de plano gráfico de ter sido com êxito **publicado**, podem ser atribuído a uma subscrição dentro do grupo de gestão que foram salvos. Este passo é onde os parâmetros são fornecidos para que cada implementação da cópia do exemplo de plano gráfico exclusivo.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione um ou mais das subscrições que estão no grupo de gestão é guardado sua cópia do exemplo de plano gráfico para. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada um usando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome do plano gráfico.
       Altere conforme necessário, ou deixe como está.
     - **Localização**: Selecione uma região para a identidade gerida a ser criado em. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão da definição de esquema**: Escolher uma **publicado** versão da sua cópia do exemplo de esquema.

   - Atribuição de bloqueio

     Selecione o bloqueio de esquema definição para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ geridos a opção de identidade.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para ver uma lista completa ou parâmetros de artefacto e suas descrições, veja [tabela de parâmetros de artefacto](#artifact-parameters-table).

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página. A atribuição do esquema é criada e começa a implementação de artefactos. Implementação demora aproximadamente uma hora. Para verificar o estado da implementação, abra a atribuição do esquema.

> [!WARNING]
> O serviço de esquemas do Azure e os exemplos de esquema incorporados estão **gratuita**. Recursos do Azure estão [o preço por produto](https://azure.microsoft.com/pricing/). Utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para calcular o custo da execução de recursos implementados por este exemplo de esquema.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela seguinte fornece uma lista do plano gráfico de parâmetros de artefacto:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|[Preview]\: PCI v3.2.1:2018 controles de auditoria e implementar extensões de VM específicas para suportar os requisitos de auditoria|Atribuição de Política|Lista de tipos de recursos | Definição de diagnóstico de auditoria para tipos de recursos selecionado. Valor predefinido é de todos os recursos estão selecionados| 
|Localizações permitidas|Atribuição de Política|Lista de localizações permitidas|Lista de localizações de Datacenter permitido para todos os recursos a serem implantados no. Esta lista é personalizável para as localizações do Azure pretendidas globalmente. Selecione as localizações que pretende permitir.| 
|Localizações permitidas para grupos de recursos|Atribuição de Política |Localização permitida |Esta política permite-lhe restringir as localizações de que sua organização pode criar grupos de recursos no. Utilize para impor os requisitos de geoconformidade.| 
|Implementar a auditoria em servidores SQL|Atribuição de Política|Dias de retenção|Retenção de dados num número de dias. Valor predefinido é de 180 mas PCI requer 365.| 
|Implementar a auditoria em servidores SQL|Atribuição de Política|Nome do grupo de recursos para a conta de armazenamento|Auditoria escreve eventos de base de dados para uma auditoria inicie sessão na sua conta de armazenamento do Azure (será criada uma conta de armazenamento em cada região onde é criado um SQL Server que irão ser partilhado por todos os servidores nessa região).| 

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu os passos para implementar o exemplo de esquema do PCI-DSS v3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Esquema de v3.2.1 PCI-DSS - descrição geral](./index.md)
> [esquema de v3.2.1 PCI-DSS - mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
