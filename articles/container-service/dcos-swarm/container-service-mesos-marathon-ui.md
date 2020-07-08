---
title: (PRECADO) Gerir cluster Azure DC/OS com Marathon UI
description: Implemente contentores num serviço de cluster do Serviço de Contentor do Azure utilizando a IU da Web do Marathon.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b251096915506c3c7a4eebf45b6a03e24779a3d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76277793"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(PRECADO) Gerir um cluster de serviço de contentores Azure DC/OS através da Maratona web UI

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O DC/OS fornece um ambiente para implementação e dimensionamento de cargas de trabalho em cluster, abstraindo o hardware subjacente. Na parte superior do DC/OS, existe uma estrutura que gere o agendamento e a execução de cargas de trabalho de computação.

Embora existam quadros para muitas cargas de trabalho populares, este documento descreve como começar a implantar contentores com maratona. 


## <a name="prerequisites"></a>Pré-requisitos
Antes de avançarmos, necessita de um cluster DC/OS configurado no Serviço de Contentor do Azure. Também necessita de conectividade remota a este cluster. Para obter mais informações sobre estes itens, consulte os artigos seguintes:

* [Implementar um cluster do Serviço de Contentor do Azure](container-service-deployment.md)
* [Ligar a um cluster do Serviço de Contentor Azure](../container-service-connect.md)

> [!NOTE]
> Este artigo pressupõe que você está fazendo um túnel para o cluster DC/OS através do seu porto local 80.
>

## <a name="explore-the-dcos-ui"></a>Explorar a IU do DC/OS
Com um túnel Secure Shell (SSH) [estabelecido,](../container-service-connect.md)navegue para http: \/ /localhost/. Este procedimento permite carregar a IU da Web do DC/OS e mostrar as informações sobre o cluster, tais como recursos utilizados, agentes ativos e serviços em execução.

![IU do DC/OS](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Explorar a IU do Marathon
Para ver a Maratona UI, navegue para http: \/ /localhost/maratona. Neste ecrã, pode iniciar um novo contentor ou outra aplicação no cluster do DC/OS do Serviço de Contentor do Azure. Também pode ver informações sobre contentores e aplicações em execução.  

![IU do Marathon](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Implementar um contentor formatado para Docker
Para implementar um novo contentor utilizando o Marathon, clique em **Criar Aplicação** e introduza as seguintes informações nos separadores do formulário:

| Campo | Valor |
| --- | --- |
| ID |nginx |
| Memória | 32 |
| Imagem |nginx |
| Rede |Com bridge |
| Porta do Anfitrião |80 |
| Protocolo |TCP |

![IU da Nova Aplicação – Geral](./media/container-service-mesos-marathon-ui/dcos4.png)

![IU da Nova Aplicação – Contentor do Docker](./media/container-service-mesos-marathon-ui/dcos5.png)

![IU da Nova Aplicação – Deteção de Serviços e Portas](./media/container-service-mesos-marathon-ui/dcos6.png)

Se pretender mapear estaticamente a porta do contentor para uma porta no agente, terá de utilizar o Modo JSON. Para tal, mude o assistente da Nova Aplicação para **Modo JSON** utilizando o botão de alternar. Em seguida, introduza o seguinte definição na secção `portMappings` da definição da aplicação. Este exemplo vincula a porta 80 do contentor à porta 80 do agente DC/OS. Pode voltar a mudar este assistente do Modo JSON após efetuar esta alteração.

```none
"hostPort": 80,
```

![IU da Nova Aplicação – exemplo de porta 80](./media/container-service-mesos-marathon-ui/dcos13.png)

Se pretender ativar as verificações de estado de funcionamento, defina um caminho no separador **Verificações do Estado de Funcionamento**.

![Nova IU da aplicação – verificações de estado de funcionamento](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

O cluster do DC/OS é implementado com um conjunto de agentes públicos e privados. Para que o cluster possa aceder às aplicações a partir da Internet, tem de implementar as aplicações para um agente público. Para fazê-lo, selecione o separador **Opcional** do assistente da Nova Aplicação e introduza **slave_public** para **Funções dos Recursos Aceites**.

Em seguida, clique em **Criar Aplicação**.

![IU da Nova Aplicação – definição de agente público](./media/container-service-mesos-marathon-ui/dcos14.png)

De voltar à página principal do Marathon, pode ver o estado de implementação do contentor. Inicialmente, vê o estado **A implementar**. Após uma implementação bem-sucedida, o estado é alterado para **Em execução**.

![IU da página principal do Marathon – estado de implementação do contentor](./media/container-service-mesos-marathon-ui/dcos7.png)

Quando volta para a UI web DC/OS (http: \/ /localhost/), vê-se que uma tarefa (neste caso, um recipiente formatado por Docker) está a funcionar no cluster DC/OS.

![IU da Web do DC/OS – tarefa em execução no cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

Para ver que o nó de cluster que a tarefa está a executar, clique no separador **Nós**.

![IU da Web do DC/OS – nó de cluster da tarefa](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>Chegar ao recipiente

Neste exemplo, a aplicação está a decorrer num nó de agente público. Você chega à aplicação a partir da internet navegando para o agente FQDN do cluster: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com` , onde:

* **DNSPREFIX** é o prefixo DNS que forneceu quando implementou o cluster.
* **REGION** é a região no qual o grupo de recursos está localizado.

    ![Nginx da Internet](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>Próximos passos
* [Trabalhar com o DC/OS e a API do Marathon](container-service-mesos-marathon-rest.md)

* Descrição aprofundada sobre o Azure Container Service com Mesos

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
