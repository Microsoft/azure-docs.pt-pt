---
title: Tutorial - Validar um hub e rede falada em Azure usando terraforma
description: Tutorial para validar o hub e a topologia da rede com todas as redes virtuais ligadas umas às outras.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159200"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Tutorial: Validar um hub e uma rede falada em Azure usando terraforma

Neste artigo, executa os ficheiros terraformais criados no artigo anterior nesta série. O resultado é uma validação da conectividade entre as redes virtuais de demonstração.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use hCL (HashiCorp Language) para implementar o Hub VNet em topologia centrada no hub
> * Utilize o plano Terraform para verificar os recursos a implantar
> * Use terrafora aplicar para criar os recursos em Azure
> * Verifique a conectividade entre diferentes redes
> * Use terraforma para destruir todos os recursos

## <a name="prerequisites"></a>Pré-requisitos

1. [Crie um hub e falou de topologia híbrida com terraforma em Azure.](./terraform-hub-spoke-introduction.md)
1. [Criar rede virtual no local com terraforma em Azure.](./terraform-hub-spoke-on-prem.md)
1. [Crie uma rede virtual hub com terraforma em Azure.](./terraform-hub-spoke-hub-network.md)
1. [Crie um aparelho de rede virtual hub com Terraform em Azure](./terraform-hub-spoke-hub-nva.md).
1. [Criar uma rede virtual com terraforma em Azure.](./terraform-hub-spoke-spoke-network.md)

## <a name="verify-your-configuration"></a>Verifique a sua configuração

Depois de completar os [pré-requisitos,](#prerequisites)verifique se estão presentes os ficheiros config apropriados.

1. Navegue pelo [portal Azure.](https://portal.azure.com)

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

1. Executar `ls` o comando para `.tf` verificar se os ficheiros de config criados nos tutoriais anteriores estão listados:

    ![Ficheiros de demonstração terraforme config](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Implementar os recursos

1. Inicializar o fornecedor Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Resultados exemplo do comando "terraforme init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Executar `terraform plan` o comando para ver o efeito da implantação antes da execução:

    ```bash
    terraform plan
    ```
    
    ![Resultados exemplo do comando "plano terraforme"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Implementar a solução:

    ```bash
    terraform apply
    ```
    
    Introduza `yes` quando solicitado para confirmar a colocação.

    ![Resultados exemplo do comando "terraforme aplicar"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Teste o hub VNet e falou VNets

Esta secção mostra como testar a conectividade desde o ambiente simulado no local até ao hub VNet.

1. No portal Azure, navegue para o grupo de recursos **onprem-vnet-rg.**

1. No **separador onprem-vnet-rg,** selecione o VM nomeado **onprem-vm**.

1. Selecione **Ligar**.

1. Ao lado do **smLogin utilizando a conta local VM,** copie o comando **ssh** para a área de reparação.

1. A partir de um `ssh` pedido de Linux, corra para ligar ao ambiente simulado no local. Utilize a palavra-passe `on-prem.tf` especificada no ficheiro do parâmetro.

1. Executar `ping` o comando para testar a conectividade com a caixa de salto VM no hub VNet:

   ```bash
   ping 10.0.0.68
   ```

1. Executar `ping` o comando para testar a conectividade com os VMs da caixa de salto em cada um dos raios:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Para sair da sessão ssh na máquina virtual `exit` **onprem-vm,** introduza e prima &lt;Enter>.

## <a name="troubleshoot-vpn-issues"></a>Problemas de vice-presidente

Para obter informações sobre a resolução de erros VPN, consulte o artigo, [Troubleshoot uma ligação VPN híbrida](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados na série tutorial.

1. Remover os recursos declarados no plano:

    ```bash
    terraform destroy
    ```

    Introduza `yes` quando solicitado para confirmar a remoção dos recursos.

1. Mude os diretórios para o diretório dos pais:

    ```bash
    cd ..
    ```

1. Eliminar `hub-scope` o diretório (incluindo todos os seus ficheiros):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre a utilização da Terraform em Azure](/azure/terraform)