---
title: Upgrade de Público Básico para Público Padrão - Equilibrador de Carga Azure
description: Este artigo mostra-lhe como atualizar o Azure Public Load Balancer de SKU Básico para Standard SKU
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612835"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgrade Azure Public Load Balancer
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um rico conjunto de funcionalidades e alta disponibilidade através da redundância de zona. Para saber mais sobre o Balancer de Carga SKU, consulte [a tabela de comparação](./skus.md#skus).

Há duas etapas numa atualização:

1. Alterar o método de atribuição de IP de Dynamic para Static.
2. Execute o script PowerShell para completar a atualização e a migração de tráfego.

## <a name="upgrade-overview"></a>Descrição geral da atualização

Um script Azure PowerShell está disponível que faz o seguinte:

* Cria um Balanceador de Carga Standard SKU com localização que especifica no mesmo grupo de recursos do Balanceador de Carga Padrão Básico.
* Atualiza o endereço IP público de Basic SKU para Standard SKU no local.
* Copia perfeitamente as configurações do Balanceador de Carga SKU Básico para o recém-criado Balancer de Carga Padrão.
* Cria uma regra de saída padrão que permite a conectividade de saída.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O Script suporta apenas o upgrade do Balancer de Carga Pública. Para a atualização do balançor de carga básica interna, consulte [esta página](./upgrade-basicinternal-standard.md) para obter instruções.
* O método de atribuição do Endereço IP Público tem de ser alterado para "estático" antes de executar o script. 
* Se o seu Balancer de Carga não tiver nenhuma configuração IP frontal ou piscina de backend, é provável que atinja um erro que executa o script. Por favor, certifique-se de que não estão vazios.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Alterar método de atribuição do endereço IP público para estática

* **Aqui estão os nossos passos recomendados:

    1. Para fazer as tarefas neste arranque rápido, inscreva-se no [portal Azure](https://portal.azure.com).
 
    1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione o Endereço IP Público Básico associado ao **Balanceador de Carga Básica** da lista de recursos.
   
    1. Em **Definições**, **selecione Configurações**.
   
    1. Em **Atribuição**, selecione **Estática**.
    1. Selecione **Guardar**.
    >[!NOTE]
    >Para VMs que tenham IPs públicos, você precisará criar endereços IP standard primeiro onde o mesmo endereço IP não é garantido. Desassociar os VMs dos IPs básicos e associá-los aos endereços IP standard recém-criados. Em seguida, poderá seguir as instruções para adicionar VMs no pool backend do Balancer de Carga Padrão. 

* **Criar novos VMs para adicionar aos pools de backend do recém-criado Balancer de Carga Pública Padrão.**
    * Mais instruções sobre como criar VM e associá-lo ao Balanceador de Carga Padrão podem ser [consultados aqui](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Descarregue o script

Descarregue o roteiro de migração da [PowerShell Gallery.](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0)
## <a name="use-the-script"></a>Use o script

Existem duas opções para si, dependendo da configuração e preferências do ambiente PowerShell local:

* Se não tiver os módulos Azure Az instalados ou não se importar de desinstalar os módulos Azure Az, a melhor opção é utilizar a `Install-Script` opção para executar o script.
* Se precisar de manter os módulos Azure Az, a sua melhor aposta é descarregar o script e executá-lo diretamente.

Para determinar se tem os módulos Azure Az instalados, corra `Get-InstalledModule -Name az` . Se não vir nenhum módulo Az instalado, então pode usar o `Install-Script` método.

### <a name="install-using-the-install-script-method"></a>Instale usando o método Install-Script

Para utilizar esta opção, não deve ter os módulos Azure Az instalados no seu computador. Se estiverem instalados, o seguinte comando apresenta um erro. Pode desinstalar os módulos Azure Az, ou utilizar a outra opção para descarregar o script manualmente e executá-lo.
  
Execute o script com o seguinte comando:

`Install-Script -Name AzurePublicLBUpgrade`

Este comando também instala os módulos Az necessários.  

### <a name="install-using-the-script-directly"></a>Instale usando o script diretamente

Se tiver alguns módulos Azure Az instalados e não os conseguir desinstalar (ou não quiser desinstalá-los), pode descarregar manualmente o script utilizando o separador **Manual descarregar** no link de descarregamento do script. O script é descarregado como um ficheiro nupkg cru. Para instalar o script a partir deste ficheiro nupkg, consulte [o Download manual do pacote](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Para executar o script:

1. Utilize `Connect-AzAccount` para ligar ao Azure.

1. Use `Import-Module Az` para importar os módulos Az.

1. Examinar os parâmetros necessários:

   * **oldRgName: [String]: Required** – Este é o grupo de recursos para o seu balanceador de carga básico existente que pretende atualizar. Para encontrar este valor de cadeia, navegue no portal Azure, selecione a fonte do Balanceador de Carga Básica e clique na **visão geral** para o balançador de carga. O Grupo de Recursos está localizado nessa página.
   * **oldLBName: [String]: Required** – Este é o nome do seu Balanceador Básico existente que pretende atualizar. 
   * **novo NOMELB: [String]: Required** – Este é o nome para o Balanceador de Carga Padrão a ser criado.
1. Executar o script usando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Criar uma regra de saída para a ligação de saída

Siga as [instruções](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) para criar uma regra de saída para que possa
* Defina o NAT de saída do zero.
* Dimensione e afina o comportamento do NAT existente.

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de V1 para v2?

Sim. Ver [Ressalvas/Limitações.](#caveatslimitations)

### <a name="how-long-does-the-upgrade-take"></a>Quanto tempo demora a atualização?

Normalmente demora cerca de 5 a 10 minutos para o script terminar e pode demorar mais tempo dependendo da complexidade da configuração do Balancer de Carga. Portanto, tenha em mente o tempo de inatividade e planeie o fracasso, se necessário.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também muda o tráfego do meu Balancer de Carga Básica para o recém-criado Balancer de Carga Padrão?

Sim. O script Azure PowerShell não só atualiza o endereço IP público, copia a configuração de Basic para Standard Load Balancer, mas também migra VM para trás do recém-criado Balancer de Carga Pública Padrão também. 

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
