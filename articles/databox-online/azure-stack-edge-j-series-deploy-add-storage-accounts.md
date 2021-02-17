---
title: Tutorial para transferir dados para conta de armazenamento com Azure Stack Edge Pro GPU| Microsoft Docs
description: Saiba como adicionar e conectar-se às contas de armazenamento local e edge no dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to storage accounts on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: b346cf37a8275b1783e94e0f51859b8c87eb9b67
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546827"
---
# <a name="tutorial-transfer-data-via-storage-accounts-with-azure-stack-edge-pro-gpu"></a>Tutorial: Transferir dados através de contas de armazenamento com Azure Stack Edge Pro GPU 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este tutorial descreve como adicionar e ligar às contas de armazenamento no seu dispositivo Azure Stack Edge Pro. Depois de ter adicionado as contas de armazenamento, o Azure Stack Edge Pro pode transferir dados para o Azure.

Este procedimento pode demorar cerca de 30 minutos a ser concluído.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma conta de armazenamento
> * Ligue-se à conta de armazenamento

 
## <a name="prerequisites"></a>Pré-requisitos

Antes de adicionar contas de armazenamento ao Azure Stack Edge Pro, certifique-se de que:

- Instalou o seu dispositivo físico como descrito no [Install Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

- Ativou o dispositivo físico como descrito no [Ativar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).


## <a name="add-an-edge-storage-account"></a>Adicione uma conta de armazenamento Edge

Para criar uma conta de armazenamento Edge, faça o seguinte procedimento:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]


## <a name="connect-to-the-edge-storage-account"></a>Ligue-se à conta de armazenamento Edge

Agora pode ligar-se ao Edge storage REST APIs em *http* ou *https*.

- *Https* é a forma segura e recomendada.
- *Http* é usado ao ligar redes fidedignas.

## <a name="connect-via-http"></a>Conecte-se via http

A ligação ao armazenamento edge REST APIs sobre http requer os seguintes passos:

- Adicione o serviço VIP consistente Azure e o ponto final de serviço blob ao anfitrião remoto
- Verificar a ligação 

Cada um destes passos é descrito nas seguintes secções.

### <a name="add-device-ip-address-and-blob-service-endpoint-to-the-remote-client"></a>Adicione o endereço IP do dispositivo e o ponto final do serviço blob ao cliente remoto

[!INCLUDE [Add device IP and blob service endpoint to host file](../../includes/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint.md)]


### <a name="verify-connection"></a>Verificar ligação

Para verificar a ligação, normalmente necessitaria das seguintes informações (pode variar) recolhidas no passo anterior:

- Nome da conta de armazenamento.
- Chave de acesso à conta de armazenamento.
- Ponto final de serviço blob.

Já tem o nome da conta de armazenamento e o ponto final do serviço blob. Pode obter a chave de acesso à conta de armazenamento ligando-se ao dispositivo através do Azure Resource Manager utilizando um cliente Azure PowerShell.

Siga os passos em [Ligar ao dispositivo através do Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md). Uma vez que tenha assinado no dispositivo local APIs através do Azure Resource Manager, obtenha a lista de contas de armazenamento no dispositivo. Execute o seguinte cmdlet:

`Get-AzureRMStorageAccount`

A partir da lista das contas de armazenamento do dispositivo, identifique a conta de armazenamento para a qual necessita da chave de acesso. Note o nome da conta de armazenamento e o grupo de recursos.

Abaixo é mostrada uma saída de amostra:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccount

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime          ProvisioningState EnableHttpsTrafficOnly
------------------ ----------------- -------- -------     ----    ---------- ------------          ----------------- ----------------------
myasetiered1       myasetiered1      DBELocal StandardLRS Storage            11/27/2019 7:10:12 PM Succeeded         False
```

Para obter a chave de acesso, executar o seguinte cmdlet:

`Get-AzureRmStorageAccountAccessKey`

Abaixo é mostrada uma saída de amostra:

```azurepowershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasetiered1
Name: myasetiered1

KeyName Value    Permissions                                                                                
------- -----    -----------                                                                                
key1    Jb2brrNjRNmArFcDWvL4ufspJjlo+Nie1uh8Mp4YUOVQNbirA1uxEdHeV8Z0dXbsG7emejFWI9hxyR1T93ZncA==        Full
key2    6VANuHzHcJV04EFeyPiWRsFWnHPkgmX1+a3bt5qOQ2qIzohyskIF/2gfNMqp9rlNC/w+mBqQ2mI42QgoJSmavg==        Full
```

Copie e guarde esta chave. Utilizará esta chave para verificar a ligação utilizando o Azure Storage Explorer.

Para verificar se a ligação está estabelecida com sucesso, utilize o Storage Explorer para anexar a uma conta de armazenamento externo. Se não tiver o Storage Explorer, [descarregue o Storage Explorer.](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409)

[!INCLUDE [Verify connection using Storage Explorer](../../includes/azure-stack-edge-gateway-verify-connection-storage-explorer.md)]


## <a name="connect-via-https"></a>Conecte-se via https

A ligação ao armazenamento Azure Blob REST APIs sobre https requer os seguintes passos:

- Obtenha o seu certificado de ponto final blob
- Importar o certificado no cliente ou anfitrião remoto
- Adicione o dispositivo IP e blob ponto final de serviço ao cliente ou anfitrião remoto
- Configurar e verificar a ligação

Cada um destes passos é descrito nas seguintes secções.

### <a name="get-certificate"></a>Obter certificado

O acesso ao armazenamento blob sobre HTTPS requer um certificado SSL para o dispositivo. Também irá enviar este certificado para o seu dispositivo Azure Stack Edge Pro como ficheiro *.pfx* com uma chave privada anexada ao mesmo. Para obter mais informações sobre como criar (apenas para fins de teste e dev) e fazer o upload destes certificados para o seu dispositivo Azure Stack Edge Pro, aceda a:

- [Crie o certificado de ponta final blob](azure-stack-edge-gpu-manage-certificates.md#create-certificates-optional).
- [Faça o upload do certificado de ponto final blob](azure-stack-edge-gpu-manage-certificates.md#upload-certificates).
- [Certificados de importação no cliente que acede ao dispositivo.](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)

### <a name="import-certificate"></a>Importar o certificado

Se utilizar o Azure Storage Explorer para se ligar às contas de armazenamento do dispositivo, também precisa de importar certificado para o Storage Explorer em formato PEM. No ambiente Windows, o *.cer* codificado base-64 é o mesmo que o formato PEM.

Tome as seguintes medidas para importar os certificados no Azure Storage Explorer:

1. Certifique-se de que o Azure Storage Explorer está a direcionar as APIs da Pilha de Azure. Vá a **Editar > Target Azure Stack APIs**. Quando solicitado, reinicie o Storage Explorer para que a alteração entre em vigor.

2. Para importar certificados SSL, vá à **Editar > certificados SSL > Certificados de Importação.**

  
   ![Cert de importação no Explorador de Armazenamento](./media/azure-stack-edge-j-series-deploy-add-storage-accounts/import-cert-storage-explorer-1.png) 

3. Navegue e forneça os certificados de assinatura e blob. Tanto a cadeia de assinaturas como o certificado blob devem estar em formato PEM, o mesmo que o formato codificado base64 no sistema Windows. Será notificado de que os certificados foram importados com sucesso.


### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Adicionar endereço IP do dispositivo e ponto final de serviço blob

Siga os mesmos passos para [adicionar o endereço IP do dispositivo e o ponto final do serviço blob ao ligar *em http*](#add-device-ip-address-and-blob-service-endpoint-to-the-remote-client).

### <a name="configure-and-verify-connection"></a>Configurar e verificar a ligação

Siga os passos para [configurar e verifique a ligação que utilizou durante a ligação *em http*](#verify-connection). A única diferença é que deve deixar a *opção Utilizar http* sem ser verificada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os seguintes tópicos Azure Stack Edge Pro:

> [!div class="checklist"]
> * Adicionar uma conta de armazenamento
> * Ligar a uma conta de armazenamento

Para aprender a transformar os seus dados utilizando o Azure Stack Edge Pro, avance para o próximo tutorial:

> [!div class="nextstepaction"]
> [Transforme dados com Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)


