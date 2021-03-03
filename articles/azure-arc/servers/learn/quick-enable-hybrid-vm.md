---
title: Ligue a máquina híbrida com servidores ativados do Arco Azure
description: Saiba como conectar e registar a sua máquina híbrida com servidores ativados Azure Arc.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: c52b8d1f7098a7a2a88a9770a3b768b7fea31775
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646831"
---
# <a name="quickstart-connect-hybrid-machines-with-azure-arc-enabled-servers"></a>Quickstart: Ligue máquinas híbridas com servidores ativados Azure Arc

[Os servidores ativados pelo Azure Arc](../overview.md) permitem gerir e governar as suas máquinas Windows e Linux hospedadas em ambientes no local, borda e multicloud. Neste arranque rápido, irá implementar e configurar o agente Máquina Conectada na sua máquina Windows ou Linux hospedada fora do Azure para gestão por servidores ativados pela Arc.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Implantação dos servidores ativados por Arc, o agente híbrido da máquina conectada requer que tenha permissões de administrador na máquina para instalar e configurar o agente. No Linux, utilizando a conta raiz, e no Windows, com uma conta que é membro do grupo de Administradores Locais.

* Antes de começar, certifique-se de rever os [pré-requisitos](../agent-overview.md#prerequisites) do agente e verificar o seguinte:

    * A sua máquina-alvo está a executar um [sistema operativo](../agent-overview.md#supported-operating-systems)suportado.

    * A sua conta é concedida a atribuição às [funções exigidas do Azure.](../agent-overview.md#required-permissions)

    * Se a máquina ligar através de uma firewall ou servidor proxy para comunicar através da Internet, certifique-se de que os URLs [listados](../agent-overview.md#networking-configuration) não estão bloqueados.

    * Os servidores ativados Azure Arc suportam apenas as regiões especificadas [aqui.](../overview.md#supported-regions)

> [!WARNING]
> O nome de hospedeiro Linux ou nome do computador Windows não pode utilizar uma das palavras ou marcas registadas reservadas no nome, caso contrário, tentar registar a máquina conectada com a Azure falhará. Consulte [os erros de nome de recursos reservados](../../../azure-resource-manager/templates/error-reserved-resource-name.md) resolvem uma lista das palavras reservadas.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registar fornecedores de recursos Azure

Os servidores ativados Azure Arc dependem dos seguintes fornecedores de recursos Azure na sua subscrição para utilizar este serviço:

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

Registe-os utilizando os seguintes comandos:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Gerar script de instalação

O script para automatizar o download, instalação e estabelecer a ligação com a Azure Arc, está disponível a partir do portal Azure. Para concluir o processo, faça o seguinte:

1. Lance o serviço Azure Arc no portal Azure clicando em **todos os serviços,** procurando e selecionando **Servidores - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Pesquisa de servidores ativados arc em todos os serviços" border="false":::

1. Na página **Servers - Azure Arc,** selecione **Adicionar** na parte superior esquerda.

1. Na página **de método Selecione uma** página de método, selecione os **servidores Add utilizando azulejos de script interativos** e, em seguida, selecione **Gerar script**.

1. Na página **de script 'Gerar',** selecione o grupo de subscrição e recursos onde pretende que a máquina seja gerida dentro do Azure. Selecione um local Azure onde os metadados da máquina serão armazenados. Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.

1. Na página **Pré-Requisitos,** reveja as informações e, em seguida, selecione **Seguinte: Detalhes do recurso**.

1. Na página **de detalhes do recurso,** forneça o seguinte:

    1. Na lista de down-down do **grupo de recursos,** selecione o grupo de recursos a partir do quais a máquina será gerida.
    1. Na lista de drop-down da **Região,** selecione a região Azure para armazenar os metadados dos servidores.
    1. Na lista de **suspensos do sistema Operativo,** selecione o sistema operativo que o script está configurado para ser executado.
    1. Se a máquina estiver a comunicar através de um servidor proxy para se ligar à internet, especifique o endereço IP do servidor proxy ou o nome e número de porta que a máquina utilizará para comunicar com o servidor proxy. Introduza o valor no `http://<proxyURL>:<proxyport>` formato.
    1. Selecione **Seguinte: Tags**.

1. Na página **Tags,** reveja as etiquetas de **localização físicas** padrão sugeridas e introduza um valor, ou especifique uma ou mais **tags personalizadas** para suportar os seus padrões.

1. Selecione **Seguinte: Descarregue e execute o script**.

1. Na página de **descarregamento e execução** do script, reveja as informações do resumo e, em seguida, selecione **Download**. Se ainda precisar de fazer alterações, selecione **Anterior**.

## <a name="install-the-agent-using-the-script"></a>Instale o agente usando o script

### <a name="windows-agent"></a>Agente do Windows

1. Faça login no servidor.

1. Abra uma ômada ômada 64 bits de comando PowerShell.

1. Altere para a pasta ou partilhe para a seguinte cópia do script e execute-o no servidor executando o `./OnboardingScript.ps1` script.

### <a name="linux-agent"></a>Agente Linux

1. Para instalar o agente Linux na máquina-alvo que pode comunicar diretamente ao Azure, executar o seguinte comando:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Se a máquina-alvo comunicar através de um servidor proxy, executar o seguinte comando:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a ligação com o Azure Arc

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja a sua máquina no [portal Azure](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Uma ligação de máquina bem sucedida" border="false":::

## <a name="next-steps"></a>Passos seguintes

Agora que ativou a sua máquina híbrida Linux ou Windows e está ligado com sucesso ao serviço, está pronto para permitir que a Azure Policy compreenda a conformidade no Azure.

Para saber como identificar os servidores ativados pelo Azure Arc que não têm o agente Log Analytics instalado, continue ao tutorial:

> [!div class="nextstepaction"]
> [Criar uma atribuição de política para identificar recursos não conformes](tutorial-assign-policy-portal.md)
