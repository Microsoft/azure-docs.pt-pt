---
title: Use máquinas virtuais Azure Data Science
description: Saiba como ligar-se a uma Máquina Virtual Azure Data Science (DSVM) para estender a potência de computação disponível para visualização de Cadernos Azure.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 291f1ac093568f50ad6146b70fa1ef69263b7b5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85830316"
---
# <a name="use-azure-data-science-virtual-machines"></a>Use máquinas virtuais Azure Data Science

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Por padrão, os projetos funcionam no nível **de Computação Livre,** que está limitado a 4 GB de memória e 1 GB de dados para prevenir abusos. Pode contornar estas limitações utilizando uma máquina virtual diferente que a provisionou numa subscrição do Azure. Para este efeito, a melhor escolha é uma Máquina Virtual Azure Data Science (DSVM) utilizando a máquina virtual de ciência de dados para a imagem **de Linux (Ubuntu).** Tal DSVM vem pré-configurado com tudo **o** que precisa para os Cadernos Azure e aparece automaticamente na lista run drop-down em Azure Notebooks.

> [!Note]
> Os Cadernos Azure são suportados apenas em DSVMs criados com a imagem em Linux Ubuntu. Os blocos não são suportados nas imagens Do Windows 2012, Windows 2016 ou Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Criar uma instância DSVM

Para criar uma nova instância DSVM, siga as instruções da [Create a Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Para obter mais informações, incluindo detalhes sobre preços, consulte [as Máquinas Virtuais da Ciência dos Dados.](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

## <a name="connect-to-the-dsvm"></a>Ligue-se ao DSVM

Assim que for criado o DSVM, selecione **a** lista de drop-down run no painel de instrumentos do projeto Azure Notebooks e selecione a instância DSVM apropriada. A lista de down-down mostra casos de DSVM se as seguintes condições forem verdadeiras:

- Você está assinado em Azure Notebooks com uma conta que usa Azure Ative Directory (AAD), como uma conta da empresa.
- A sua conta está ligada a uma subscrição do Azure.
- Tem uma ou mais máquinas virtuais nessa subscrição, com pelo menos acesso ao Leitor, que utiliza a Máquina Virtual de Ciência de Dados para a imagem de Linux (Ubuntu).)

![Casos de Máquina Virtual de Ciência de Dados na lista de drop-down no painel de instrumentos do projeto](media/project-compute-tier-dsvm.png)

Ao selecionar uma instância DSVM, os Cadernos Azure podem solicitar-lhe as credenciais específicas da máquina utilizadas quando criou o VM.

> [!Important]
> O nome de utilizador deve ser minúsculo para usá-lo com o JupyterHub.

Se alguma das condições não forem satisfeitas, ainda pode ligar-se ao DSVM. Na lista de drop-down, selecione a opção **Direct Compute,** que lhe pede um nome (para aparecer na lista), o endereço IP e porta do VM (normalmente 8000, a porta predefinido a que o JupyterHub ouve) e as credenciais VM:

![Solicitação para recolher informações do servidor para a opção Direct Compute](media/project-compute-tier-direct.png)

Obtém estes valores na página DSVM no portal Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Aceder aos ficheiros Azure Notebooks do DSVM

O acesso ao sistema de ficheiros é suportado para as versões DSVM 19.06.15 ou posterior. Para verificar a versão, ligue primeiro ao seu DSVM via SSH e, em seguida, execute o seguinte comando: `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (tem de utilizar o endereço IP exato aqui indicado). O número da versão é mostrado na saída para "versão".

Para preservar a paridade dos caminhos de arquivo com o nível **de Computação Livre,** só é possível abrir um projeto de cada vez num DSVM. Para abrir um novo projeto, primeiro tens de encerrar o projeto aberto.

Quando um projeto é executado num VM, os ficheiros são montados no diretório de raiz do servidor Jupyter (o diretório mostrado no JupyterHub), substituindo os ficheiros Azure Notebooks predefinidos. Quando desliga o VM utilizando o botão **De desligar** no UI do portátil, o Azure Notebooks restaura os ficheiros predefinidos.

![Botão de encerramento em Cadernos Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Criar novos utilizadores de DSVM

Se vários utilizadores partilharem um DSVM, pode evitar bloquear-se uns aos outros criando e utilizando um utilizador DSVM para cada utilizador de um portátil:

1. No [portal Azure,](https://portal.azure.com)navegue para a sua máquina virtual.
1. Em **Suporte + resolução de problemas** na margem esquerda, selecione **Redefinir a palavra-passe**.
1. Introduza um novo **nome de utilizador**. O nome de utilizador deve ser minúsculo para usá-lo com o JupyterHub. Introduza uma senha. Em seguida, selecione **Update**. (Os nomes de utilizador existentes não são afetados.)
1. Repita o passo anterior para qualquer utilizadores adicionais.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre DSVMs sobre [Introdução a Azure Data Science Virtual Machines](/azure/machine-learning/data-science-virtual-machine/overview).
