---
title: 'Pré-visualização: Implementar um VM de lançamento fidedigno'
description: Implemente um VM que usa lançamento fidedigno.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565921"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Implementar um VM com lançamento fidedigno ativado (pré-visualização)

[Lançamento confiável](trusted-launch.md) é uma forma de melhorar a segurança da [geração 2](generation-2.md) VMs. O lançamento de confiança protege contra técnicas de ataque avançadas e persistentes, combinando tecnologias de infraestrutura como vTPM e boot seguro.

> [!IMPORTANT]
> O lançamento de confiança está atualmente em pré-visualização pública.
> 
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
>
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Implementar usando o portal

Crie uma máquina virtual com lançamento fidedigno ativado.

1. Inscreva-se no [portal](https://aka.ms/TL_preview)Azure .
   > [!NOTE] 
   > O link Portal é exclusivo da pré-visualização de lançamento confiável.
   >  
2. Procure por **Máquinas Virtuais.**
3. Em **Serviços**, selecione **Máquinas Virtuais.**
4. Na página **de máquinas Virtuais,** selecione **Adicionar** e, em seguida, selecione **máquina Virtual**.
5. Nos **detalhes do Projeto,** certifique-se de que a subscrição correta está selecionada.
6. No **grupo de Recursos,** selecione Criar um **nome novo** e digitar um nome para o seu grupo de recursos ou selecione um grupo de recursos existente a partir do dropdown.
7. Em **Detalhes de Exemplo,** escreva um nome para o nome da máquina virtual e escolha uma região que suporte [o lançamento fidedigno](trusted-launch.md#public-preview-limitations).
8. Under **Image**, selecione uma imagem gen 2 [que suporta o lançamento fidedigno](trusted-launch.md#public-preview-limitations). Certifique-se de que vê a seguinte mensagem: **Esta imagem suporta a pré-visualização de lançamento fidedigna. Configurar no separador Avançado**.
   > [!TIP]
   > Se não vir a versão Gen 2 da imagem que pretende no drop-down, selecione **Ver todas as imagens** e, em seguida, altere o filtro **VM Generation** para mostrar apenas imagens da Gen 2. Encontre a imagem na lista e, em seguida, use o drop-down **Select** para selecionar a versão Gen 2.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Screenshot mostrando a mensagem confirmando que esta é uma imagem gen2 que suporta o lançamento confiável.":::

13. Selecione um tamanho VM que suporte lançamento fidedigno. Consulte a lista de [tamanhos suportados.](trusted-launch.md#public-preview-limitations)
14. Preencha as informações da **conta do Administrador** e, em **seguida, as regras do porto de entrada**. 
1. Mude para o separador **Advanced** selecionando-o no topo da página.
1. Percorra a secção de **geração VM.** Certifique-se de que **a Gen 2** está selecionada.
1. Enquanto ainda está no separador **Advanced,** desloque-se até ao **lançamento do Trust** e, em seguida, selecione a caixa de **verificação de lançamento Fidedigna.** Isto fará com que apareçam mais duas opções - Arranque seguro e vTPM. Selecione as opções apropriadas para a sua implementação.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Screenshot mostrando as opções para lançamento de confiança.":::

15. Na parte inferior da página, selecione **Review + Create**
16. Na página **de máquina virtual Criar,** pode ver os detalhes sobre o VM que está prestes a implementar. Quando estiver pronto, selecione **Criar**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot da página de validação, mostrando as opções de lançamento fidedignas estão incluídas.":::


Irá demorar alguns minutos até a VM ser implementada. 

## <a name="deploy-using-a-template"></a>Implementar com um modelo

Pode implementar VMs de lançamento fidedignos utilizando um modelo de arranque rápido:

**Linux**:    
[![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Ver e atualizar

Pode ver a configuração de lançamento fidedigna para um VM existente visitando a página **geral** para o VM no portal.

Para alterar a configuração de lançamento fidedigna, no menu esquerdo, **selecione Configuração** na secção **Definições.** Pode ativar ou desativar o Secure Boot e o vTPM a partir da secção **Lançamento Fidedigno.** **Selecione Guarde** no topo da página quando terminar. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Screenshot de como alterar a configuração de lançamento fidedigna.":::

Se o VM estiver em execução, receberá uma mensagem de que o VM será reiniciado para aplicar a configuração de lançamento fidedigna modificada. Selecione **Sim** e, em seguida, aguarde que o VM reinicie para que as alterações entrem em vigor.


## <a name="verify-secure-boot-and-vtpm"></a>Verifique a bota segura e vTPM

Pode validar o arranque seguro e o vTPM na máquina virtual.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: validar se a bota segura estiver a correr

SSH para o VM e, em seguida, executar o seguinte comando: 

```bash
mokutil --sb-state
```

Se o arranque seguro estiver ativado, o comando regressará:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: validar se o vTPM estiver ativado

Aceda através de SSH à VM. Verifique se o dispositivo TPM0 está presente: 

```bash
ls /dev/tpm0
```

Se o vTPM estiver ativado, o comando regressará:

```output
/dev/tpm0
```

Se o vTPM estiver desativado, o comando regressará:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: validar que a bota segura está em execução

Ligue-se ao VM utilizando um ambiente de trabalho remoto e, em seguida, corra `msinfo32.exe` .

No painel direito, verifique se o Estado de Arranque Seguro está **LIGADO**.

## <a name="enable-the-azure-security-center-experience"></a>Ativar a experiência do Centro de Segurança Azure

Para permitir que o Azure Security Center apresente informações sobre os seus VMs de lançamento fidedignos, é necessário ativar várias políticas. A forma mais fácil de ativar as políticas é implementando este [modelo de Gestor de Recursos](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) para a sua subscrição. 

Selecione o botão abaixo para implementar as políticas para a sua subscrição:

[![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

O modelo precisa de ser implantado apenas uma vez por subscrição. Instala e amplia automaticamente `GuestAttestation` `AzureSecurity` em todos os VMs suportados. Se tiver erros, tente recolocar o modelo novamente.

Para obter vTPM e recomendar recomendações de arranque seguras para VMs de lançamento fidedignos, consulte [Adicionar uma iniciativa personalizada à sua subscrição.](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Assine coisas para Secure Boot em Linux

Em alguns casos, poderá ter de assinar coisas para o UEFI Secure Boot.  Por exemplo, pode precisar de ver [como assinar coisas para Secure Boot](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) para Ubuntu. Nestes casos, tem de introduzir as chaves de inscrição de utilitário MOK para o seu VM. Para isso, é necessário utilizar a Consola em Série Azure para aceder ao utilitário MOK.

1. Ativar a consola em série Azure para o Linux. Para mais informações, consulte [a Consola em Série para Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Faça login no [portal Azure](https://portal.azure.com).
1. Procure **por máquinas Virtuais** e selecione o seu VM da lista.
1. No menu esquerdo, em **Suporte + resolução de problemas,** selecione **Consola série**. Uma página abrir-se-á à direita, com a consola em série.
1. Inicie sessão no VM utilizando a Consola Em Série Azure. Para **iniciar sessão,** insira o nome de utilizador utilizado quando criou o VM. Por exemplo, *azureuser.* Quando solicitado, introduza a palavra-passe associada ao nome de utilizador.
1. Uma vez iniciado o login, utilize `mokutil` para importar o ficheiro de chave `.der` pública.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Reinicie a máquina a partir da Consola em Série Azure digitando `sudo reboot` . Começará uma contagem regressiva de 10 segundos.
1. Pressione para cima ou para baixo para interromper a contagem regressiva e aguarde no modo de consola UEFI. Se o temporizador não for interrompido, o processo de arranque continua e todas as alterações DO MOK são perdidas.
1. Selecione a ação apropriada no menu de utilidade MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Screenshot mostrando as opções disponíveis no menu de gestão MOK na consola em série.":::


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o lançamento fidedigno](trusted-launch.md) e [vMs da Geração 2.](generation-2.md)
