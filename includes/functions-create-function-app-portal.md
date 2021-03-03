---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9ec5920295117b1726a684bcd08534cd6917f7e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "100569797"
---
1. A partir do menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na **nova** página, selecione App de Função **computacional**  >  .

1. Na página **Basics,** utilize as definições da aplicação de função conforme especificado na tabela seguinte.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Subscrição** | A sua subscrição | A subscrição no âmbito da qual esta nova aplicação de funções é criada. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do grupo de recursos novo no qual a aplicação Function App vai ser criada. |
    | **Nome da Aplicação de Funções** | Nome globalmente exclusivo | Nome que identifica a sua aplicação Function App nova. Os carateres válidos são `a-z` (não sensível a maiúsculas e minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar ficheiros de código ou um contentor de Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um tempo de execução que suporte a sua linguagem de programação de funções favorita. A edição no portal só está disponível para o script JavaScript, PowerShell, TypeScript e C#. As funções de classe C#, Java e Python devem ser [desenvolvidas localmente.](../articles/azure-functions/functions-develop-local.md#local-development-environments)  |
    |**Versão**| Número da versão | Escolha a versão do seu tempo de execução instalado. |
    |**Região**| Região preferida | Escolha uma [região](https://azure.microsoft.com/regions/) perto de si ou perto de outros serviços a que as suas funções acedam. |

1. Selecione **Seguinte: Alojamento**. Na página **'Hospedagem',** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento para ser utilizada pela sua aplicação de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e podem conter apenas números e letras minúsculas. Também pode utilizar uma conta existente, que deve satisfazer os requisitos da [conta de armazenamento.](../articles/azure-functions/storage-considerations.md#storage-account-requirements) |
    |**Sistema operativo**| Windows | Um sistema operativo é pré-selecionado para si com base na seleção da sua pilha de tempo de execução, mas pode alterar a definição se necessário. A edição no portal só é suportada no Windows. |
    | **[Planear](../articles/azure-functions/functions-scale.md)** | **Consumo (Sem servidor)** | O plano de alojamento que define a forma como os recursos são alocados à sua aplicação Function App. No **Plano de Consumo** predefinido, os recursos são adicionados dinamicamente, conforme necessário por parte das suas funções. Neste hospedagem [sem servidor,](https://azure.microsoft.com/overview/serverless-computing/) paga-se apenas pelo tempo que as suas funções são executadas. Quando executa num plano do Serviço de Aplicações, tem de gerir o [dimensionamento da sua aplicação de funções](../articles/azure-functions/functions-scale.md).  |

1. Selecione **Seguinte : Monitorização**. Na página **'Monitor'** insira as seguintes definições.

    | Definição      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Predefinição | Cria um recurso Application Insights com o mesmo *nome app* na região suportada mais próxima. Ao expandir esta definição ou selecionar **Criar novos,** pode alterar o nome De Insights de Aplicação ou escolher uma região diferente numa [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde pretende armazenar os seus dados. |

1. Selecione **Review + crie** para rever as seleções de configuração da aplicação.

1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar** para provisão e implementar a aplicação de função.

1. Selecione o ícone **notificações** no canto superior direito do portal e observe a mensagem **de implementação conseguida.**

1. Selecione **Ir para o recurso** para ver a sua nova aplicação de funções. Também pode selecionar **Pin para painel de instrumentos.** A fixação facilita o regresso a esta função de recurso de aplicação a partir do seu dashboard.

    ![Notificação de implantação](./media/functions-create-function-app-portal/function-app-create-notification2.png)
