---
title: Upgrade de Basic Internal para Standard Internal - Azure Load Balancer
description: Este artigo mostra-lhe como atualizar o Balançador de Carga Interna Azure de SKU Básico para SKU Padrão
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050702"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Upgrade Azure Internal Load Balancer - Sem Conexão de saída necessária
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um rico conjunto de funcionalidades e alta disponibilidade através da redundância de zona. Para saber mais sobre o Balancer de Carga SKU, consulte [a tabela de comparação](./skus.md#skus).

Este artigo introduz um script PowerShell que cria um Balanceador de Carga Padrão com a mesma configuração que o Balanceador de Carga Básica, juntamente com o tráfego migrador do Balanceador de Carga Básica para o Balanceador de Carga Padrão.

## <a name="upgrade-overview"></a>Descrição geral da atualização

Um script Azure PowerShell está disponível que faz o seguinte:

* Cria um Balanceador de Carga SKU Interno Padrão no local que especifica. Note que nenhuma [ligação de saída](./load-balancer-outbound-connections.md) será fornecida pelo Balanceador De Carga Interna Padrão.
* Copia perfeitamente as configurações do Balanceador de Carga SKU Básico para o recém-criado Balanceador de Carga Padrão.
* Mover os IPs privados do Balanceador de Carga Básico para o recém-criado Balancer de Carga Padrão.
* Mover perfeitamente os VMs do pool de backend do Balanceador de Carga Básica para o pool de backend do Balanceador de Carga Padrão

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O Script só suporta a atualização do Balancer de Carga Interna onde não é necessária nenhuma ligação de saída. Se necessitar de [ligação de saída](./load-balancer-outbound-connections.md) para alguns dos seus VMs, consulte esta [página](upgrade-InternalBasic-To-PublicStandard.md) para obter instruções. 
* O Balanceador de Carga Básica tem de estar no mesmo grupo de recursos que os VMs e NICs de backend.
* Se o balanceador de carga Standard for criado numa região diferente, não poderá associar os VM existentes na região antiga ao recém-criado Balancer de Carga Padrão. Para contornar esta limitação, certifique-se de criar um novo VM na nova região.
* Se o seu Balancer de Carga não tiver nenhuma configuração IP frontal ou piscina de backend, é provável que atinja um erro que executa o script. Certifique-se de que não estão vazios.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Altere o método de atribuição ip para estática para a configuração IP frontend (ignore este passo se já estiver estático)

1. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, em seguida, selecione o seu Balancer de Carga Básica na lista de recursos.

2. Em **Definições**, selecione **a configuração IP frontend** e selecione a primeira configuração IP frontal. 

3. Para **atribuição**, selecione **Estática**

4. Repita o passo 3 para todas as configurações IP frontend do Balanceador de Carga Básica.


## <a name="download-the-script"></a>Descarregue o script

Descarregue o roteiro de migração da [PowerShell Gallery.](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0)
## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências do ambiente PowerShell local:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos Azure Az, a melhor opção é utilizar a `Install-Script` opção para executar o script.
* Se precisar de manter os módulos Azure Az, a sua melhor aposta é descarregar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure Az instalados, corra `Get-InstalledModule -Name az` . Se não vir nenhum módulo Az instalado, então pode usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o seguinte comando apresenta um erro. Pode desinstalar os módulos Azure Az, ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzureILBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não quiser desinstalá-los), pode descarregar manualmente o script utilizando o separador **Manual descarregar** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte [o Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

1. Examinar os parâmetros necessários:

   * **rgName: [String]: Required** – Este é o grupo de recursos para o seu balanceador de carga básico existente e novo Balanceador de Carga Padrão. Para encontrar este valor de cadeia, navegue no portal Azure, selecione a fonte do Balanceador de Carga Básica e clique na **visão geral** para o balançador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – Este é o nome do seu Balanceador Básico existente que pretende atualizar. 
   * **newlocation: [String]: Required** – Este é o local em que o Balanceador de Carga Padrão será criado. Recomenda-se herdar a mesma localização do Equilibrador de Carga Básica escolhido para o Balanceador de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **novo NOMELB: [String]: Required** – Este é o nome para o Balanceador de Carga Padrão a ser criado.
1. Executar o script usando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de V1 para v2?

Sim. Ver [Ressalvas/Limitações.](#caveatslimitations)

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também muda o tráfego do meu Balancer de Carga Básica para o recém-criado Balancer de Carga Padrão?

Sim, migra o tráfego. Se quiser migrar o tráfego pessoalmente, utilize [este script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) que não move VMs para si.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
