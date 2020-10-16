---
title: Ligue ao Arco Azure ativado sql instância gerida
description: Ligue ao Arco Azure ativado sql instância gerida
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939197"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Ligue ao Arco Azure ativado sql instância gerida

Este artigo explica como pode ligar-se ao seu Azure Arc ativado SQL Managed Instance. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Ver Azure Arc habilitado a SQL Gestão de Instâncias

Para visualizar o Arco Azure ativado SQL Gestão De Instância e os pontos finais externos usam o seguinte comando:

```console
azdata arc sql mi list
```

A saída deve ser assim:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Se estiver a utilizar AKS ou kubeadm ou OpenShift, etc., pode copiar o número de IP e porta externo a partir daqui e conectá-lo usando a sua ferramenta preferida para se ligar a uma instância SQL Sever/Azure SQL, como o Azure Data Studio ou o SQL Server Management Studio.  No entanto, se estiver a utilizar o VM de arranque rápido, consulte abaixo para obter informações especiais sobre como ligar-se a esse VM de fora de Azure. 

> [!NOTE]
> As suas políticas corporativas podem bloquear o acesso ao IP e ao porto, especialmente se isso for criado na nuvem pública.

## <a name="connect"></a>Ligar 

Conecte-se com o Azure Data Studio, SQL Server Management Studio ou SQLCMD

Abra o Azure Data Studio e ligue-se ao seu caso com o endereço IP do ponto final externo e o número de porta acima. Se estiver a utilizar um VM Azure, necessitará do endereço IP _público,_ que é identificável utilizando a [nota especial sobre as instalações de máquinas virtuais Azure](#special-note-about-azure-virtual-machine-deployments).

Por exemplo:

- Servidor: 52.229.9.30,30913
- Nome de utilizador: sa
- Palavra-passe: a sua senha SQL especificada no tempo de fornecimento

> [!NOTE]
> Pode utilizar o Azure Data Studio [para visualizar os dashboards de instância geridos sql](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Para ligar utilizando SQLCMD ou Linux ou Windows pode utilizar um comando como este. Introduza a palavra-passe SQL quando solicitado:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Nota especial sobre as implementações de máquinas virtuais Azure

Se estiver a utilizar uma máquina virtual Azure, o endereço IP do ponto final não mostrará o endereço IP público. Para localizar o endereço IP externo, utilize o seguinte comando:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Em seguida, pode combinar o endereço IP público com a porta para fazer a sua ligação.

Também poderá ser necessário expor a porta do exemplo sql através do gateway de segurança da rede (NSG). Para permitir o tráfego através do (NSG) terá de adicionar uma regra que pode fazer utilizando o seguinte comando.

Para definir uma regra, terá de saber o nome do seu NSG que pode descobrir usando o comando abaixo:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Uma vez que tenha o nome do NSG, pode adicionar uma regra de firewall usando o seguinte comando. Os valores de exemplo aqui criam uma regra NSG para a porta 30913 e permite a ligação a partir de **qualquer** endereço IP de origem.  Esta não é uma boa prática de segurança!  Para garantir uma maior segurança, pode definir um valor --source-address-prefixes específico do endereço IP do cliente ou um intervalo de endereços IP que englobe os endereços IP da equipa ou organização.

Substitua o valor do `--destination-port-ranges` parâmetro abaixo pelo número de porta que obteve do `azdata sql instance list` comando F acima.

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Passos seguintes

- [Ver os dashboards de instância geridos sql](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Ver SQL Gestdion Instance no portal Azure](view-arc-data-services-inventory-in-azure-portal.md)
