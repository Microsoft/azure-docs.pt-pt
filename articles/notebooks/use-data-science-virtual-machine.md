---
title: Utilizar máquinas virtuais azure data science
description: Saiba como se conectar a uma Máquina Virtual de Ciência de Dados Azure (DSVM) para alargar a potência da computação disponível para a Pré-visualização de Cadernos Azure.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898409"
---
# <a name="use-azure-data-science-virtual-machines"></a>Utilizar máquinas virtuais azure data science

Por padrão, os projetos funcionam no nível **De Computação Livre,** que está limitado a 4 GB de memória e 1 GB de dados para prevenir abusos. Pode contornar estas limitações utilizando uma máquina virtual diferente que disponibilizou numa subscrição Azure. Para o efeito, a melhor escolha é uma Máquina Virtual de Ciência de Dados Azure (DSVM) utilizando a máquina virtual de ciência de dados para a imagem **linux (Ubuntu).** Tal DSVM vem pré-configurado com tudo o que precisa para os Cadernos Azure e aparece automaticamente na lista de drop-down **run** em Cadernos Azure.

> [!Note]
> Os Cadernos Azure são suportados apenas em DSVMs criados com a imagem de Linux Ubuntu. Os cadernos não são suportados nas imagens Windows 2012, Windows 2016 ou Linux CentOS.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Criar uma instância DSVM

Para criar uma nova instância DSVM, siga as instruções sobre [Criar um Ubuntu Data Science VM](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Para mais informações, incluindo detalhes sobre preços, consulte [Máquinas Virtuais](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)de Ciência de Dados .

## <a name="connect-to-the-dsvm"></a>Ligue-se ao DSVM

Assim que for criado o DSVM, selecione a lista de drop-down **run** no painel de instrumentos do projeto Dos Cadernos Azure e selecione a instância DSVM apropriada. A lista de abandono mostra casos de DSVM se as seguintes condições forem verdadeiras:

- Está inscrito nos Cadernos Azure com uma conta que utiliza o Azure Ative Directory (AAD), como uma conta da empresa.
- A sua conta está ligada a uma subscrição do Azure.
- Tem uma ou mais máquinas virtuais nessa subscrição, com pelo menos acesso ao Leitor, que utiliza a Máquina Virtual de Ciência de Dados para imagem Linux (Ubuntu).

![Data Science Virtual Machine apresenta-se na lista de drop-down no painel de instrumentos do projeto](media/project-compute-tier-dsvm.png)

Quando selecionar uma instância DSVM, os Cadernos Azure podem pedir-lhe as credenciais específicas da máquina utilizadas quando criou o VM.

> [!Important]
> O nome de utilizador deve ser minúsculo para usá-lo com o JupyterHub.

Se alguma das condições não for satisfeita, ainda pode ligar-se ao DSVM. Na lista de drop-down, selecione a opção **Compute Direto,** que lhe indica um nome (para mostrar na lista), o endereço IP e a porta do VM (normalmente 8000, a porta padrão a que o JupyterHub ouve) e as credenciais VM:

![Solicitação para recolher informações do servidor para a opção Computação Direta](media/project-compute-tier-direct.png)

Obtém estes valores a partir da página DSVM no portal Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Aceder aos ficheiros de Cadernos Azure do DSVM

O acesso ao sistema de ficheiros é suportado para versões DSVM 19.06.15 ou posteriores. Para verificar a versão, ligue-se primeiro ao seu DSVM `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` via SSH e, em seguida, execute o seguinte comando: (deve utilizar o endereço IP exato mostrado aqui). O número da versão é mostrado na saída para "versão".

Para preservar a paridade dos caminhos de ficheiros com o nível **De Computação Livre,** só é possível abrir um projeto de cada vez num DSVM. Para abrir um novo projeto, tem de encerrar primeiro o projeto aberto.

Quando um projeto é executado num VM, os ficheiros são montados no diretório raiz do servidor Jupyter (o diretório mostrado no JupyterHub), substituindo os ficheiros de Notebooks Deprepreto Sino. Quando desliga o VM utilizando o botão **'Desligar'** no portátil UI, os Cadernos Azure restauram os ficheiros predefinidos.

![Botão de paragem em cadernos Azure](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Criar novos utilizadores dSVM

Se vários utilizadores partilharem um DSVM, pode evitar bloquear-se uns aos outros criando e utilizando um utilizador DSVM para cada utilizador de portátil:

1. No [portal Azure,](https://portal.azure.com)navegue para a sua máquina virtual.
1. Sob **suporte + resolução de problemas** na margem esquerda, selecione **Reset password**.
1. Introduza um novo **nome de utilizador**. O nome de utilizador deve ser minúsculo para usá-lo com o JupyterHub. Introduza uma senha. Em seguida, selecione **Atualizar**. (Os nomes de utilizador existentes não são afetados.)
1. Repita o passo anterior para qualquer utilizador adicional.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre DSVMs na introdução às máquinas virtuais da Ciência dos [Dados Do Azure.](/azure/machine-learning/data-science-virtual-machine/overview)
