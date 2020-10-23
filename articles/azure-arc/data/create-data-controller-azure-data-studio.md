---
title: Criar controlador de dados no Azure Data Studio
description: Criar controlador de dados no Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 208c9b4172719b876766f0c4d07a17caa24bfd63
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310955"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Criar controlador de dados no Azure Data Studio

Pode criar um controlador de dados utilizando o Azure Data Studio através do assistente de implementação e dos cadernos.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de ter acesso a um cluster Kubernetes e ter o seu ficheiro kubeconfig configurado para apontar para o cluster Kubernetes para o que pretende implementar.
- Tem de [instalar as ferramentas do cliente,](install-client-tools.md) incluindo o **Azure Data Studio,** as extensões do Azure Data Studio chamadas **Azure Arc** e **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** .
- Tem de fazer login no Azure no Azure Data Studio.  Para isso: digite CTRL/Comando + SHIFT + P para abrir a janela de texto de comando e **digitar Azure**.  Escolha **Azure: Inscreva-se**.   No painel, que aparece clique no ícone + no topo direito para adicionar uma conta Azure.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Utilize o Assistente de Implementação para criar o controlador de dados Azure Arc

Siga estes passos para criar um controlador de dados Azure Arc utilizando o assistente de implementação.

1. No Azure Data Studio, clique no separador Connections na navegação à esquerda.
2. Clique no **botão ...** na parte superior do painel 'Ligações' e escolha **Nova Implementação...**
3. No novo assistente de implementação, escolha O **Controlador de Dados Azure Arc,** verifique a caixa de verificação de aceitação da licença e, em seguida, clique no botão **Select** na parte inferior.
4. Utilize o ficheiro kubeconfig padrão ou selecione outro.  Clique em **Seguinte**.
5. Escolha um contexto de cluster Kubernetes. Clique em **Seguinte**.
6. Escolha um ficheiro de perfil de configuração de implementação dependendo do cluster Kubernetes alvo. **Clique em Seguinte**.
8. Escolha o grupo de subscrição e recursos pretendido.
9. Introduza um nome para o controlador de dados e para o espaço de nome em que o controlador de dados será criado.  

> [!NOTE]
> Se o espaço de nome já existir, será usado se o espaço de nomes já não contiver outros objetos Kubernetes - pods, etc.  Se o espaço de nome não existir, será feita uma tentativa de criar o espaço de nome.  Criar um espaço de nome num cluster Kubernetes requer privilégios de administrador de cluster kubernetes.  Se não tiver privilégios de administrador de cluster kubernetes, peça ao seu administrador de cluster Kubernetes para executar os primeiros passos no Controlador de dados Create um controlador de dados utilizando o artigo [de ferramentas nativas de Kubernetes](./create-data-controller-using-kubernetes-native-tools.md) que são necessários para ser executados por um administrador kubernetes antes de completar este assistente.

> [!NOTE]
> Nota: o controlador de dados e o nome do espaço de nome serão utilizados para criar um recurso personalizado no cluster Kubernetes, pelo que devem estar em conformidade com [as convenções de nomeação de Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Selecione uma localização do Azure.
   
> [!NOTE]
> A localização Azure selecionada aqui é a localização em Azure onde os *metadados* sobre o controlador de dados e as instâncias de base de dados que gere serão armazenados.  As instâncias do controlador de dados e da base de dados serão realmente tripuladas no seu cluster Kubernetes onde quer que isso esteja.

11.  Introduza um nome de utilizador e senha e confirme a palavra-passe para a conta de utilizador do administrador do controlador de dados.

> [!NOTE]
> A palavra-passe deve ter pelo menos 8 caracteres de comprimento.

1.  Clique em **Seguinte**.
2.  Reveja e clique **em Script para Notebook**.
3.  **Reveja o caderno gerado.**  Esquídee quaisquer alterações necessárias, tais como nomes de classe de armazenamento ou tipos de serviço.
4.  Clique em **Executar Tudo** no topo do caderno.

## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do controlador levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  O exemplo comanda abaixo assumir que criou um controlador de dados e espaço de nome Kubernetes com o nome 'arc'.  Se utilizar um nome diferente de espaço de nome/controlador de dados, pode substituir 'arco' pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo.  Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)
