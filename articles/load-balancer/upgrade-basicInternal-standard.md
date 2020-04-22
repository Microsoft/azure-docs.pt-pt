---
title: Upgrade de Basic Internal para Standard Internal - Azure Load Balancer
description: Este artigo mostra-lhe como atualizar o Equilíbrio de Carga Interna Azure de Basic SKU para Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770376"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgrade Azure Internal Load Balancer - Sem necessidade de ligação de saída
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um conjunto rico de funcionalidades e alta disponibilidade através de redundância de zona. Para saber mais sobre o Load Balancer SKU, consulte a [tabela de comparação](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Este artigo introduz um script PowerShell que cria um Balancer de Carga Padrão com a mesma configuração do Equilíbrio de Carga Básico juntamente com o tráfego migratório do Equilíbrio de Carga Básica para o Equilíbrio de Carga Padrão.

## <a name="upgrade-overview"></a>Descrição geral da atualização

Está disponível um script Azure PowerShell que faz o seguinte:

* Cria um Balancer de carga SKU Interno Padrão no local que especifica. Note que nenhuma [ligação](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) de saída não será fornecida pelo Balancer De carga Interna Padrão.
* Copia perfeitamente as configurações do Equilíbrio de Carga SKU Básico para o recém-criado Standard Load Balancer.
* Mover perfeitamente os IPs privados do Equilíbrio de Carga Básico para o recém-criado Equilibrador de Carga Padrão.
* Mover perfeitamente os VMs da piscina de backend do Equilíbrio de Carga Básica para a piscina de backend do Equilibrador de Carga Padrão

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O script apenas suporta a atualização do Balancer de Carga Interna onde não é necessária nenhuma ligação de saída. Se necessitar de [ligação de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) para alguns dos seus VMs, consulte esta [página](upgrade-InternalBasic-To-PublicStandard.md) para obter instruções. 
* Se o equilibrador de carga Standard for criado numa região diferente, não poderá associar os VMs existentes na região antiga ao recém-criado Standard Load Balancer. Para contornar esta limitação, certifique-se de criar um novo VM na nova região.
* Se o seu Balancer de Carga não tiver qualquer configuração IP frontal ou piscina de backend, é provável que acerte um erro ao executar o script. Certifique-se de que não estão vazios.

## <a name="download-the-script"></a>Descarregue o script

Descarregue o script de migração da [Galeria PowerShell.](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0)
## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências locais do ambiente PowerShell:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos `Install-Script` Azure Az, a melhor opção é usar a opção de executar o script.
* Se precisa de manter os módulos Azure Az, a sua melhor aposta é baixar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure `Get-InstalledModule -Name az`Az instalados, corra . Se não vir nenhum módulo Az instalado, então `Install-Script` pode utilizar o método.

### <a name="install-using-the-install-script-method"></a>Instalar utilizando o método Instalação-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o comando seguinte apresenta um erro. Pode desinstalar os módulos Azure Az ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Executar o script com o seguinte comando:

`Install-Script -Name AzureILBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale diretamente o script

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não os pretender desinstalar), pode descarregar manualmente o script utilizando o separador **Descarregamento Manual** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte o [Manual Package Download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Utilize `Import-Module Az` para importar os módulos Az.

1. Examinar os parâmetros necessários:

   * **rgName: [String]: Required** – Este é o grupo de recursos para o seu equilíbrio de carga básico existente e novo Equilíbrio de Carga Padrão. Para encontrar este valor de cadeia, navegue para o portal Azure, selecione a sua fonte de Equilíbrio de Carga Básica e clique na **visão geral** para o equilibrador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – Este é o nome do seu Equilíbrio Básico existente que pretende atualizar. 
   * **newlocation: [String]: Required** – Este é o local em que o Balancer de Carga Padrão será criado. Recomenda-se herdar a mesma localização do Equilibrador básico de carga escolhido para o Equilíbrio de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **newLBName: [String]: Required** – Este é o nome para o Balancer de Carga Padrão a ser criado.
1. Executar o script utilizando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de v1 para v2?

Sim. Ver [Ressalvas/Limitações](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também muda o tráfego do meu Equilíbrio de Carga Básico para o recém-criado Standard Load Balancer?

Sim, migra o tráfego. Se quiser migrar o tráfego pessoalmente, use [este guião](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) que não move VMs para si.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Tive alguns problemas com o uso deste guião. Como posso conseguir ajuda?
  
Pode enviar um slbupgradesupport@microsoft.come-mail para, abrir um caso de suporte com o Suporte Azure, ou fazer os dois.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Balancer de Carga Padrão](load-balancer-overview.md)
