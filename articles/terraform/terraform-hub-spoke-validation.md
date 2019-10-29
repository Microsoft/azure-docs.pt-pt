---
title: Tutorial – validar uma rede hub e spoke no Azure usando o Terraform
description: Tutorial para validar a topologia de rede hub e spoke com todas as redes virtuais conectadas entre si.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: b0b761fcd79f7129befefa37ce11d9c70cf7cb96
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969340"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Tutorial: validar uma rede hub e spoke no Azure usando o Terraform

Neste artigo, você executa os arquivos Terraform criados no artigo anterior desta série. O resultado é uma validação da conectividade entre as redes virtuais de demonstração.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use a HCL (linguagem HashiCorp) para implementar a VNet do Hub na topologia hub-spoke
> * Usar o plano Terraform para verificar os recursos a serem implantados
> * Usar o Terraform Apply para criar os recursos no Azure
> * Verificar a conectividade entre redes diferentes
> * Usar o Terraform para destruir todos os recursos

## <a name="prerequisites"></a>Pré-requisitos

1. [Crie uma topologia de rede híbrida de Hub e spoke com Terraform no Azure](./terraform-hub-spoke-introduction.md).
1. [Crie uma rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md).
1. [Crie uma rede virtual de Hub com Terraform no Azure](./terraform-hub-spoke-hub-network.md).
1. [Crie um dispositivo de rede virtual de Hub com o Terraform no Azure](./terraform-hub-spoke-hub-nva.md).
1. [Crie redes virtuais spoke com Terraform no Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Verifique sua configuração

Depois de concluir os [pré-requisitos](#prerequisites), verifique se os arquivos de configuração apropriados estão presentes.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se não tiver selecionado um ambiente anteriormente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Mude para o diretório novo:

    ```bash
    cd hub-spoke
    ```

1. Execute o comando `ls` para verificar se os arquivos de configuração `.tf` criados nos tutoriais anteriores estão listados:

    ![Arquivos de configuração de demonstração do Terraform](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Implantar os recursos

1. Inicialize o provedor Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Exemplo de resultados do comando "Terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Execute o comando `terraform plan` para ver o efeito da implantação antes da execução:

    ```bash
    terraform plan
    ```
    
    ![Exemplo de resultados do comando "plano Terraform"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Implantar a solução:

    ```bash
    terraform apply
    ```
    
    Insira `yes` quando solicitado a confirmar a implantação.

    ![Exemplo de resultados do comando "Terraform Apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testar a VNet do Hub e o spoke VNets

Esta seção mostra como testar a conectividade do ambiente simulado local para a VNet do Hub.

1. Na portal do Azure, navegue até o grupo de recursos **onlocal-vnet-RG** .

1. Na guia **onlocal-vnet-RG** , selecione a VM chamada **local-VM**.

1. Selecione **Ligar**.

1. Ao lado do logon de texto **usando a conta local da VM**, copie o comando **SSH** para a área de transferência.

1. Em um prompt do Linux, execute `ssh` para se conectar ao ambiente simulado local. Use a senha especificada no arquivo de parâmetro `on-prem.tf`.

1. Execute o comando `ping` para testar a conectividade com a VM Jumpbox na VNet do Hub:

   ```bash
   ping 10.0.0.68
   ```

1. Execute o comando `ping` para testar a conectividade com as VMs Jumpbox em cada spoke:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Para sair da sessão SSH na máquina virtual da **VM local** , insira `exit` e pressione &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Solucionar problemas de VPN

Para obter informações sobre como resolver erros de VPN, consulte o artigo [solucionar problemas de uma conexão VPN híbrida](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados na série de tutoriais.

1. Remova os recursos declarados no plano:

    ```bash
    terraform destroy
    ```

    Insira `yes` quando solicitado a confirmar a remoção dos recursos.

1. Altere os diretórios para o diretório pai:

    ```bash
    cd ..
    ```

1. Exclua o diretório `hub-scope` (incluindo todos os seus arquivos):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)