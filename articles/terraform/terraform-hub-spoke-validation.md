---
title: Validar uma rede hub- and -spoke com o Terraform no Azure
description: Tutorial para validar a topologia de rede hub- and -spoke com todas as redes virtuais ligadas entre si.
services: terraform
ms.service: terraform
keywords: terraform, hub- and -spoke, redes, redes de híbridas, devops, máquina virtual, do azure, o vnet peering,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: eac86ac5d56d4aef4a8220ff9b40f053d241a356
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411418"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Tutorial: Validar uma rede hub- and -spoke com o Terraform no Azure

Neste artigo, executa os ficheiros de terraform criados no artigo anterior desta série. O resultado é uma validação da conetividade entre as redes virtuais de demonstração.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Utilize o HCL (HashiCorp Language) para implementar a VNet do Hub na topologia hub-and-spoke
> * Utilize o Terraform plano para verificar os recursos a serem implantados
> * Utilize Terraform aplicam-se para criar os recursos no Azure
> * Verificar a conectividade entre várias redes
> * Utilize o Terraform para destruir todos os recursos

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar um hub- and -spoke topologia de rede híbrida com o Terraform no Azure](./terraform-hub-spoke-introduction.md).
1. [Criar rede virtual no local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md).
1. [Criar uma rede virtual do concentrador com o Terraform no Azure](./terraform-hub-spoke-hub-network.md).
1. [Criar uma aplicação de rede virtual de hub com o Terraform no Azure](./terraform-hub-spoke-hub-nva.md).
1. [Criar redes virtuais com o Terraform no Azure um spoke](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Verificar a configuração

Depois de concluir o [pré-requisitos](#prerequisites), certifique-se de que os ficheiros de configuração adequados estão presentes.

1. Navegue para o [portal do Azure](http://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Mude para o diretório novo:

    ```bash
    cd hub-spoke
    ```

1. Executar o `ls` comando para verificar se o `.tf` estão listados os ficheiros de configuração criados nos tutoriais anteriores:

    ![Ficheiros de configuração de demonstração do Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Implementar os recursos

1. Inicialize o fornecedor do Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Resultados de exemplo do comando de "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Execute o `terraform plan` comando para ver o efeito da implantação antes da execução:

    ```bash
    terraform plan
    ```
    
    ![Resultados de exemplo do comando de "plano de terraform"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Implemente a solução:

    ```bash
    terraform apply
    ```
    
    Introduza `yes` quando lhe for pedido para confirmar a implementação.

    ![Resultados de exemplo do comando "terraform aplicar"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testar a VNet do hub- and -spoke VNets

Esta secção mostra como testar a conectividade entre o ambiente simulado no local e a VNet do hub.

1. No portal do Azure, navegue para o **onprem-vnet-rg** grupo de recursos.

1. Na **onprem-vnet-rg** separador, selecione a VM com o nome **onprem-vm**.

1. Selecione **Ligar**.

1. Junto do texto **início de sessão com a conta local de VM**, copie a **ssh** comando na área de transferência.

1. A partir de uma linha de comandos do Linux, execute `ssh` para estabelecer ligação ao ambiente no local simulado. Utilize a palavra-passe especificada no `on-prem.tf` ficheiro de parâmetros.

1. Execute o `ping` comandos para testar a conectividade à VM da jumpbox na VNet do hub:

   ```bash
   ping 10.0.0.68
   ```

1. Execute o `ping` comandos para testar a conectividade para a jumpbox VMs em cada spoke:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Para sair do ssh sessão sobre o **onprem-vm** máquina virtual, introduza `exit` e prima &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Resolver problemas VPN

Para obter informações sobre como resolver erros VPN, consulte o artigo [resolver problemas de uma ligação VPN de híbrida](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine os recursos criados da série de tutoriais.

1. Remova os recursos declarados no plano de:

    ```bash
    terraform destroy
    ```

    Introduza `yes` quando lhe for pedido para confirmar a remoção dos recursos.

1. Altere os diretórios para o diretório principal:

    ```bash
    cd ..
    ```

1. Eliminar o `hub-scope` diretório (incluindo todos os seus ficheiros):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre como utilizar o Terraform no Azure](/azure/terraform)