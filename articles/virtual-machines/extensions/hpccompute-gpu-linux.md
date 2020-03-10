---
title: Extensão do condutor da GPU da NVIDIA - VMs Azure Linux
description: Extensão Microsoft Azure para instalar os pilotos gpu da NVIDIA em VMs computacionais da série N executando Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383350"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Extensão do condutor da GPU da NVIDIA para o Linux

## <a name="overview"></a>Descrição geral

Esta extensão instala os condutores de GPU da NVIDIA em VMs da série N Linux. Dependendo da família VM, a extensão instala os condutores CUDA ou GRID. Quando instala os controladores NVIDIA utilizando esta extensão, está a aceitar e a concordar com os termos do Contrato de Licença de Utilizador Final da [NVIDIA](https://go.microsoft.com/fwlink/?linkid=874330). Durante o processo de instalação, o VM pode reiniciar para completar a configuração do controlador.

As instruções sobre a instalação manual dos controladores e as versões suportadas atuais estão disponíveis [aqui](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).
Também está disponível uma extensão para instalar controladores GPU da NVIDIA em [VMs da série N do Windows](hpccompute-gpu-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Esta extensão suporta os seguintes distros de SO, dependendo do suporte do condutor para a versão oS específica.

| Distribuição | Versão |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão Microsoft Azure para os controladores GPU da NVIDIA requer que o VM alvo esteja ligado à internet e tenha acesso.

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Propriedades

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.HpcCompute | Cadeia de caracteres |
| tipo | NvidiaGpuDriverLinux | Cadeia de caracteres |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Definições

Todas as definições são opcionais. O comportamento predefinido é não atualizar o núcleo se não for necessário para a instalação do controlador, instalar o mais recente controlador suportado e o conjunto de ferramentas CUDA (conforme aplicável).

| Nome | Descrição | Default Value | Valores válidos | Tipo de Dados |
| ---- | ---- | ---- | ---- | ---- |
| atualizandoOS | Atualize o núcleo mesmo que não seja necessário para a instalação do condutor | false | VERDADEIRO, FALSO | valor booleano |
| driverVersion | NV: Versão do controlador grid<br> VERSÃO NC/ND: versão de kit de ferramentas CUDA. Os mais recentes controladores para o CUDA escolhido saem automaticamente instalados. | mais recente | GRELHA: "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | Cadeia de caracteres |
| instalarCUDA | Instale o conjunto de ferramentas CUDA. Apenas relevante para vMs da série NC/ND. | true | VERDADEIRO, FALSO | valor booleano |


## <a name="deployment"></a>Implementação


### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager 

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que requerem a configuração da implementação do post.

A configuração do JSON para uma extensão de máquina virtual pode ser aninhada dentro do recurso de máquina virtual ou colocada na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação da configuração do JSON afeta o valor do tipo e nome do recurso. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/resource-manager-template-child-resource.md) 

O exemplo que se segue pressupõe que a extensão está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>CLI do Azure

O exemplo que se segue espelha os exemplos acima do Gestor de Recursos Azure e powerShell e também adiciona configurações personalizadas como exemplo para a instalação do controlador não predefinido. Especificamente, atualiza o kernel OS e instala um controlador específico da versão cuda toolkit.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
  }'
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolver Problemas

Os dados sobre o estado das implementações de extensões podem ser recuperados a partir do portal Azure e utilizando o Azure PowerShell e o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Códigos de saída

| Código de Saída | Significado | Ação possível |
| :---: | --- | --- |
| 0 | Operação bem sucedida |
| 1 | Utilização incorreta da extensão | Verifique o registo de saída de execução |
| 10 | Serviços de Integração Linux para Hiper-V e Azure não disponíveis ou instalados | Verifique a saída de lspci |
| 11 | NVIDIA GPU não encontrada neste tamanho VM | Use um [tamanho VM suportado e OS](../linux/n-series-driver-setup.md) |
| 12 | Oferta de imagem não suportada |
| 13 | Tamanho VM não suportado | Use um VM da série N para implementar |
| 14 | Operação infrutífera | Verifique o registo de saída de execução |


### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina virtual para Linux](features-linux.md).

Para obter mais informações sobre VMs da série N, consulte [gpu otimizado tamanhos](../linux/sizes-gpu.md)de máquinavirtual .
