---
title: Hub de gestão
description: Gerir as suas ligações, configuração de controlo de fontes e propriedades de autoria global no centro de gestão da Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 02/01/2021
ms.openlocfilehash: b4b9ecef84f8ffcc82107299ad6603466380d1c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371503"
---
# <a name="management-hub-in-azure-data-factory"></a>Centro de gestão na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

O centro de gestão, acedido pelo *separador Manage* no Azure Data Factory UX, é um portal que acolhe ações de gestão global para a sua fábrica de dados. Aqui, pode gerir as suas ligações a lojas de dados e cálculos externos, configuração de controlo de fontes e definições de gatilho.

## <a name="manage-connections"></a>Gerir ligações

### <a name="linked-services"></a>Serviços ligados

Os serviços ligados definem as informações de ligação para a Azure Data Factory para se conectar a lojas de dados externas e ambientes computacional. Para mais informações, consulte [os conceitos de serviços ligados.](concepts-linked-services.md) A criação, edição e eliminação de serviços ligados são feitas no centro de gestão.

![Gerir serviços ligados](media/author-management-hub/management-hub-linked-services.png)

### <a name="integration-runtimes"></a>Tempos de integração

Um tempo de integração é uma infraestrutura de computação usada pela Azure Data Factory para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para mais informações, saiba sobre [conceitos de integração em tempo de execução.](concepts-integration-runtime.md) No centro de gestão, pode criar, excluir e monitorizar os tempos de integração.

![Gerir runtimes de integração](media/author-management-hub/management-hub-integration-runtime.png)

## <a name="manage-source-control"></a>Gerir o controlo de origem

### <a name="git-configuration"></a>Configuração de Git

Pode visualizar/editar todas as informações relacionadas com Git nas definições de configuração git no centro de gestão. 

As últimas informações publicadas também estão listadas e podem ajudar a compreender o compromisso preciso, que foi publicado pela última vez/ implantado em ambientes. Também pode ser útil ao fazer Hot Fixes na produção.

Para mais informações, saiba mais sobre [o controlo de fontes na Azure Data Factory.](source-control.md)

![Gerir git repo](media/author-management-hub/management-hub-git.png)

### <a name="parameterization-template"></a>Modelo de parametrização

Para anular os parâmetros do modelo gerados do Gestor de Recursos ao publicar a partir do ramo de colaboração, pode gerar ou editar um ficheiro de parâmetros personalizados. Para obter mais informações, aprenda a [usar parâmetros personalizados no modelo de Gestor de Recursos.](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template) O modelo de parametrização só está disponível quando se trabalha num repositório de git. Se o *arm-template-parameters-definition.jsno* ficheiro não existir no ramo de trabalho, a edição do modelo predefinido gera-o.

![Gerir params personalizados](media/author-management-hub/management-hub-custom-parameters.png)

## <a name="manage-authoring"></a>Gerir a autoria

### <a name="triggers"></a>Acionadores

Os gatilhos determinam quando deve ser iniciada uma conduta. Atualmente, os gatilhos podem estar no horário do relógio de parede, operar num intervalo periódico ou depender de um evento. Para mais informações, saiba sobre [a execução do gatilho.](concepts-pipeline-execution-triggers.md#trigger-execution) No centro de gestão, pode criar, editar, excluir ou ver o estado atual de um gatilho.

![Screenshot que mostra onde criar, editar, excluir, nem ver o estado atual de um gatilho.](media/author-management-hub/management-hub-triggers.png)

### <a name="global-parameters"></a>Parâmetros globais

Os parâmetros globais são constantes em toda uma fábrica de dados que pode ser consumida por um oleoduto em qualquer expressão. Para mais informações, saiba sobre [os parâmetros globais.](author-global-parameters.md)

![Criar parâmetros globais](media/author-global-parameters/create-global-parameter-3.png)

## <a name="next-steps"></a>Passos seguintes

Aprenda a [configurar um repositório de git](source-control.md) para o seu ADF


