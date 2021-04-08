---
title: Upgrade de Basic Internal para Standard Public - Azure Load Balancer
description: Este artigo mostra-lhe como atualizar o Equilibrador de Carga Interna Básico Azure para o Balanceador de Carga Pública Padrão
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3394754f2829018f7862b3775f8ab2cb2d07d005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98051365"
---
# <a name="upgrade-azure-internal-load-balancer---outbound-connection-required"></a>Upgrade Azure Internal Load Balancer - Ligação de saída necessária
[O Azure Standard Load Balancer](load-balancer-overview.md) oferece um rico conjunto de funcionalidades e alta disponibilidade através da redundância de zona. Para saber mais sobre o Balancer de Carga SKU, consulte [a tabela de comparação](./skus.md#skus). Uma vez que o Balanceador de Carga Interna Padrão não fornece ligação de saída, fornecemos uma solução para criar um Balanceador de Carga Pública Padrão.

Há quatro fases numa atualização:

1. Migrar a configuração para o Balanceador de Carga Pública Padrão
2. Adicione VMs para apoiar piscinas do Balanceador de Carga Pública Padrão
3. Criar regras NSG para subnet/VMs que devem ser abstivedas de/para a Internet

Este artigo abrange a migração de configuração. Adicionar VMs a piscinas de backend pode variar dependendo do seu ambiente específico. No entanto, [são fornecidas](#add-vms-to-backend-pools-of-standard-load-balancer)algumas recomendações gerais de alto nível.

## <a name="upgrade-overview"></a>Descrição geral da atualização

Um script Azure PowerShell está disponível que faz o seguinte:

* Cria um Balanceador de Carga Pública Standard SKU no grupo de recursos e localização que especifica.
* Copia perfeitamente as configurações do Balanceador de Carga Interna SKU básico para o recém-criado Balancer de Carga Pública Padrão.
* Cria uma regra de saída que permite a conectividade da saída.

### <a name="caveatslimitations"></a>Ressalvas\Limitações

* O script suporta a atualização do Balancer de Carga Interna onde é necessária uma ligação de saída. Se não for necessária a ligação de saída para qualquer um dos VMs, consulte [esta página](upgrade-basicInternal-standard.md) para obter as melhores práticas.
* O Balanceador de Carga Padrão tem um novo endereço público. É impossível mover os endereços IP associados ao Balanceador de Carga Interna Básico existente de forma perfeita para o Balanceador de Carga Pública Padrão, uma vez que têm DIFERENTES SKUs.
* Se o balanceador de carga Standard for criado numa região diferente, não poderá associar os VM existentes na região antiga ao recém-criado Balancer de Carga Padrão. Para contornar esta limitação, certifique-se de criar um novo VM na nova região.
* Se o seu Balancer de Carga não tiver nenhuma configuração IP frontal ou piscina de backend, é provável que atinja um erro que executa o script.  Certifique-se de que não estão vazios.

## <a name="download-the-script"></a>Descarregue o script

Descarregue o roteiro de migração da [PowerShell Gallery.](https://www.powershellgallery.com/packages/AzureLBUpgrade/2.0)
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
   * **newrgName: [String]: Required** – Este é o grupo de recursos no qual o Balanceador de Carga Padrão será criado. Pode ser um novo grupo de recursos ou um grupo existente. Se escolher um grupo de recursos existente, note que o nome do Balanceador de Carga tem de ser único dentro do grupo de recursos. 
   * **newlocation: [String]: Required** – Este é o local em que o Balanceador de Carga Padrão será criado. Recomendamos herdar a mesma localização do Equilibrador de Carga Básica escolhido para o Balanceador de Carga Padrão para uma melhor associação com outros recursos existentes.
   * **novo NOMELB: [String]: Required** – Este é o nome para o Balanceador de Carga Padrão a ser criado.
1. Executar o script usando os parâmetros apropriados. Pode levar 5 a 7 minutos para terminar.

    **Exemplo**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newrgName &quot;test_userInput3_rg&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Adicione VMs para apoiar piscinas do Balanceador de Carga Padrão

Em primeiro lugar, verifique duas vezes se o script criou com sucesso um novo Balanceador de Carga Pública Padrão com a configuração exata migrada do seu Balancer de Carga Pública Básica. Pode verificar isto a partir do portal Azure.

Certifique-se de que envia uma pequena quantidade de tráfego através do Balanceador de Carga Padrão como teste manual.
  
Aqui estão alguns cenários de como adicionar VMs para apoiar piscinas do recém-criado Balanceador de Carga Pública Padrão pode ser configurado, e nossas recomendações para cada um:

* **Mover VMs existentes de piscinas de backend do antigo Balancer de Carga Pública Básica para apoiar piscinas do recém-criado Balanceador de Carga Pública Padrão**.
    1. Para fazer as tarefas neste arranque rápido, inscreva-se no [portal Azure](https://portal.azure.com).
 
    1. Selecione **todos os recursos** no menu esquerdo e, em seguida, selecione o **recém-criado Balanceador de Carga Padrão** da lista de recursos.
   
    1. Em **Definições**, selecione **piscinas backend**.
   
    1. Selecione o pool de backend que corresponde ao pool de backend do Balanceador de Carga Básica, selecione o seguinte valor: 
      - **Máquina Virtual**: Desça e selecione os VMs do pool de backend correspondente do Balanceador de Carga Básica.
    1. Selecione **Guardar**.
    >[!NOTE]
    >Para VMs que tenham IPs públicos, você precisará criar endereços IP standard primeiro onde o mesmo endereço IP não é garantido. Desassociar os VMs dos IPs básicos e associá-los aos endereços IP standard recém-criados. Em seguida, poderá seguir as instruções para adicionar VMs no pool backend do Balancer de Carga Padrão. 

* **Criar novos VMs para adicionar aos pools de backend do recém-criado Balancer de Carga Pública Padrão.**
    * Mais instruções sobre como criar VM e associá-lo ao Balanceador de Carga Padrão podem ser [consultados aqui](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Criar uma regra de saída para a ligação de saída

Siga as [instruções](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) para criar uma regra de saída para que possa
* Defina o NAT de saída do zero.
* Dimensione e afina o comportamento do NAT existente.

### <a name="create-nsg-rules-for-vms-which-to-refrain-communication-from-or-to-the-internet"></a>Criar regras NSG para VMs que abster a comunicação de ou para a Internet
Se quiser abster-se de evitar que o tráfego de Internet chegue aos seus VMs, pode criar uma [regra NSG](../virtual-network/manage-network-security-group.md) na Interface de Rede dos VMs.

## <a name="common-questions"></a>Perguntas comuns

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existem limitações com o script Azure PowerShell para migrar a configuração de V1 para v2?

Sim. Ver [Ressalvas/Limitações.](#caveatslimitations)

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>O script Azure PowerShell também muda o tráfego do meu Balancer de Carga Básica para o recém-criado Balancer de Carga Padrão?

N.º O script Azure PowerShell apenas migra a configuração. A migração real do tráfego é da sua responsabilidade e do seu controlo.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre o Balanceador de Carga Padrão](load-balancer-overview.md)
