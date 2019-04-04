---
title: Dimensionar nós de adicionar o Azure Stack | Documentos da Microsoft
description: Adicione nós para unidades de escala no Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 17da540bd6077b8e045f125fd3cf13dc0e043000
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882043"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack"></a>Adicionar nós de unidade de escala adicionais no Azure Stack

Operadores do Azure Stack podem aumentar a capacidade geral de uma unidade de dimensionamento existente através da adição de um computador físico adicional. O computador físico é também referido como um nó de unidade de escala. Cada novo nó de unidade de escala que é adicionar tem de ser homogénea no tipo da CPU, memória e o número de disco e tamanho para os nós que já estão presentes na unidade de escala.

Para adicionar um nó de unidade de escala, atuar no Azure Stack e executar as ferramentas do fabricante de equipamento de hardware (OEM). As ferramentas de OEM é executado no anfitrião de ciclo de vida de hardware (HLH) para se certificar de que o novo computador físico corresponde ao mesmo nível de firmware que nós existentes.

O diagrama de fluxo que se segue mostra o processo geral para adicionar um nó de unidade de escala.

![Adicionar fluxo de unidade de escala](media/azure-stack-add-scale-node/add-node-flow.png) &#42; *se o seu fornecedor de hardware de OEM enacts a colocação de rack do servidor físico e atualiza o firmware variam com base no seu contrato de suporte.*

A operação para adicionar um novo nó pode demorar várias horas ou dias para ser concluído.

> [!Note]  
> Não tente qualquer uma das seguintes operações enquanto uma operação de nó de unidade de escala de add já está em curso:
>
>  - Atualizar o Azure Stack
>  - Rotação de certificados
>  - Parar o Azure Stack
>  - Nó de unidade de escala de reparação


## <a name="add-scale-unit-nodes"></a>Adicionar nós de unidade de escala

Os seguintes passos são uma visão geral de como adicionar um nó. Não siga estes passos sem consultar primeiro a documentação de expansão de capacidade fornecida pelo OEM.

1. Colocar o novo servidor físico em bastidor e instalar os cabos-lo adequadamente. 
2. Ative as portas de comutador físico e ajuste listas de controlo de acesso (ACLs), se aplicável.
3. Configurar o endereço IP correto no controlador de gestão da placa base (BMC) e aplicar todas as definições de BIOS pela documentação fornecida pelo OEM.
4. Aplicam-se a linha de base do firmware atual para todos os componentes usando as ferramentas que são fornecidas pelo fabricante do hardware com o HLH.
5. Execute a operação de nó de adição no portal de administração do Azure Stack.
6. Valide que a operação de nó de add for concluída com êxito. Para fazer isso, verifique os [ **Status** da unidade de escala](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Adicionar o nó

Pode utilizar o portal de administração ou o PowerShell para adicionar novos nós. A operação de nó de adicionar primeiro adiciona o novo nó de unidade de escala como a capacidade de computação disponíveis e, em seguida, aumenta automaticamente a capacidade de armazenamento. A capacidade se expande automaticamente porque o Azure Stack é um sistema hiperconvergido onde *computação* e *armazenamento* dimensionados em conjunto.

### <a name="use-the-admin-portal"></a>Utilizar o portal de administração

1. Inicie sessão no portal de administração do Azure Stack como um operador do Azure Stack.
2. Navegue para **+ criar um recurso** > **capacidade** > **nó de unidade de escala**.
   ![Nó de unidade de escala](media/azure-stack-add-scale-node/select-node1.png)
3. Sobre o **adicionar nó** painel, selecione a *região*e, em seguida, selecione o *unidade de escala* que pretende adicionar o nó para. Especifique também a *endereço de IP de BMC* para o nó de unidade de escala que está a adicionar. Só pode adicionar um nó por vez.
   ![Adicionar detalhes do nó](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Utilizar o PowerShell

Utilize o **New-AzsScaleUnitNodeObject** cmdlet para adicionar um nó.  

Antes de utilizar qualquer um dos scripts do PowerShell de exemplo abaixo, substitua os valores *nomes de nó* e *endereços IP* com valores do seu ambiente do Azure Stack.

  > [!Note]  
  > Quando atribuir nomes a um nó é necessário manter o nome de menos de 15 carateres de comprimento. Também não é possível utilizar um nome que contém um espaço ou contiver qualquer um dos seguintes carateres: `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Adicione um nó:**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Monitor de adicionar as operações de nó 
Pode utilizar o portal de administração ou o PowerShell para obter o estado da operação de nó de adicionar. Adicione operações de nó podem demorar várias horas a dias para concluir.

### <a name="use-the-admin-portal"></a>Utilizar o portal de administração 
Para monitorizar a adição de um novo nó, no portal de administração, reveja a unidade de escala ou objetos de nó de unidade de dimensionamento. Para tal, aceda a **gestão da região** > **unidades de escala**. Em seguida, selecione a unidade de escala ou o nó de unidade de escala que pretende rever. 

### <a name="use-powershell"></a>Utilizar o PowerShell
O estado de unidade de escala e nós de unidade de escala pode ser obtido através do PowerShell da seguinte forma:
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>Estado para a operação de nó de adição 
**Para uma unidade de escala:**

|Estado               |Descrição  |
|---------------------|---------|
|A executar              |Todos os nós estão a participar ativamente na unidade de escala.|
|Parada              |O nó de unidade de escala é baixo ou inacessível.|
|Expandir            |Atualmente estão a ser adicionados um ou mais nós de unidade de escala, como a capacidade de computação.|
|Configurar o armazenamento  |A capacidade de computação foi expandida e a configuração de armazenamento está em execução.|
|Requer remediação |Foi detetado um erro que exige um ou mais nós de unidade de escala ser reparado.|


**Para um nó de unidade de escala:**

|Estado                |Descrição  |
|----------------------|---------|
|A executar               |O nó está a participar ativamente na unidade de escala.|
|Parada               |O nó não está disponível.|
|A adicionar                |O nó está ativamente a ser adicionado para a unidade de escala.|
|A reparar             |O nó está ativamente a ser reparado.|
|Manutenção           |O nó está em pausa e nenhuma carga de trabalho de utilizador do Active Directory está em execução. |
|Requer remediação  |Foi detetado um erro que exige que o nó ser reparado.|


## <a name="troubleshooting"></a>Resolução de problemas
Seguem-se problemas comuns detetados quando adicionar um nó. 

**Cenário 1:**  A operação de nó de unidade de escala de adicionar falhar, mas um ou mais nós são listadas com o estado parado.  
- Remediação: Utilize a operação de reparação para reparar um ou mais nós. Apenas uma operação de reparação único pode ser executados em simultâneo.

**Cenário 2:** Um ou mais nós de unidade de escala foram adicionados, mas falha na expansão do armazenamento. Neste cenário, o objeto de nó de unidade de escala comunica um Estado de execução, mas a tarefa de configuração de armazenamento não é iniciada.  
- Remediação: Utilize o ponto final com privilégios para rever o estado de funcionamento do armazenamento ao executar o seguinte cmdlet do PowerShell:
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Cenário 3:** É recebido um alerta que indica a tarefa de escalamento horizontal de armazenamento falhou.  
- Remediação: Neste caso, a tarefa de configuração de armazenamento falhou. Esse problema tem de contactar o suporte.


## <a name="next-steps"></a>Passos Seguintes 
[Adicionar endereços IP públicos](azure-stack-add-ips.md) 
