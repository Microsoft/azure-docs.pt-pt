---
title: Como implantar o serviço de gerenciamento de certificados do cofre OPC-Azure | Microsoft Docs
description: Como implantar o serviço de gerenciamento de certificados do cofre OPC do zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1625f0e6f9bfe8297cae2770e63107bf4f19f95e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012988"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Compilar e implantar o serviço de gerenciamento de certificados do compartimento OPC

Este artigo explica como implantar o serviço de gerenciamento de certificados do Vault do OPC no Azure.

> [!NOTE]
> Para obter mais informações sobre detalhes de implantação e instruções, consulte o [repositório do cofre OPC](https://github.com/Azure/azure-iiot-opc-vault-service)do github.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-required-software"></a>Instalar o software necessário

Atualmente, a operação de compilação e implantação é limitada ao Windows.
Os exemplos são todos escritos para C# o .net Standard, que é necessário para criar o serviço e os exemplos para implantação.
Todas as ferramentas de que você precisa para o .net Standard vêm com as ferramentas de .Net Core. Veja [aqui](https://docs.microsoft.com/dotnet/articles/core/getting-started) o que você precisa.

1. [Instale o .NET Core 2.1 +][dotnet-install].
2. [Instalar][docker-url] o Docker (opcional, somente se o Build do Docker local for necessário).
4. Instale as [ferramentas de linha de comando do Azure para PowerShell][powershell-install].
5. Inscreva-se para uma [assinatura do Azure][azure-free].

### <a name="clone-the-repository"></a>Clonar o repositório

Se você ainda não fez isso, clone este repositório GitHub.  Abra um prompt de comando ou terminal e execute:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

ou clone o repositório diretamente no Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Criar e implantar o serviço do Azure no Windows

Um script do PowerShell fornece uma maneira fácil de implantar o microserviço do cofre OPC e o aplicativo.<br>

1. Abra uma janela do PowerShell na raiz do repositório. 
3. Ir para a pasta implantar`cd deploy`
3. Escolha um nome `myResourceGroup` que seja improvável de causar um conflito com outras páginas da Web implantadas. Veja [abaixo](#website-name-already-in-use) como os nomes das páginas da Web são escolhidos com base no nome do grupo de recursos.
5. Iniciar a implantação com `.\deploy.ps1` para instalação interativa<br>
ou insira uma linha de comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se você planeja desenvolver com essa implantação, adicione `-development 1` para habilitar a interface do usuário do Swagger e implantar compilações de depuração.
6. Siga as instruções no script para entrar em sua assinatura e fornecer informações adicionais.
9. Após uma operação de compilação e implantação bem-sucedida, você deverá ver a seguinte mensagem:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

Caso você tenha problemas, siga as etapas [abaixo](#troubleshooting-deployment-failures).

8. Abra seu navegador favorito e abra a página do aplicativo:`https://myResourceGroup.azurewebsites.net`
8. Dê ao aplicativo Web e ao microserviço do compartimento OPC alguns minutos para fazer o aquecimento após a implantação. O home page da Web pode parar de ser usado pela primeira vez por até um minuto até que você obtenha as primeiras respostas.
11. Para dar uma olhada na API do Swagger aberta:`https://myResourceGroup-service.azurewebsites.net`
13. Para iniciar um servidor GDS local com dotnet iniciar `.\myResourceGroup-gds.cmd` ou com o início `.\myResourceGroup-dockergds.cmd`do Docker.

Como um anotação rápida, é possível reimplantar uma compilação com exatamente as mesmas configurações. Lembre-se de que essa operação renova todos os segredos do aplicativo e pode redefinir algumas configurações nos registros do aplicativo Azure Active Directory (AD do Azure).

Também é possível reimplantar apenas os binários do aplicativo Web. Com o parâmetro `-onlyBuild 1` , os novos pacotes zip do serviço e o aplicativo são implantados nos aplicativos Web.

Após a implantação bem-sucedida, sinta-se à vontade para começar a usar os serviços: [Como gerenciar o serviço de gerenciamento de certificados do compartimento OPC](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Excluir os serviços da assinatura

1. Entre no portal do Azure: `https://portal.azure.com`.
2. Vá para o grupo de recursos no qual o serviço foi implantado.
3. Selecione `Delete resource group` e confirme.
4. Depois de um pouco, todos os componentes de serviço implantados são excluídos.
5. Agora, vá `Azure Active Directory/App registrations`para.
6. Deve haver três registros listados para cada grupo de recursos implantado com os seguintes nomes `resourcegroup-client`: `resourcegroup-module`, `resourcegroup-service`,.
Cada registro precisa ser excluído separadamente.
7. Agora todos os componentes implantados são removidos.

## <a name="troubleshooting-deployment-failures"></a>Solucionando problemas de falhas de implantação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de usar um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos e o prefixo de URL de serviço e, dessa forma, deve estar em conformidade com os requisitos de nomenclatura de recursos.  

### <a name="website-name-already-in-use"></a>O nome do site já está em uso

É possível que o nome do site já esteja em uso.  Se você encontrar esse erro, precisará usar um nome de grupo de recursos diferente. Os nomes de host em uso pelo script de implantação são https://resourcegroupname.azurewebsites.net : https://resourgroupname-service.azurewebsites.net e.
Outros nomes de serviços são criados pela combinação de hashes de nome curtos e são pouco prováveis de entrar em conflito com outros serviços.

### <a name="azure-active-directory-azure-ad-registration"></a>Registro do Azure Active Directory (AD do Azure) 

O script de implantação tenta registrar três aplicativos do Azure AD em Azure Active Directory.  
Dependendo de suas permissões no locatário do Azure AD selecionado, essa operação poderá falhar.   Há duas opções:

1. Se você escolher um locatário do Azure AD de uma lista de locatários, reinicie o script e escolha um diferente na lista.
2. Como alternativa, implante um locatário privado do Azure AD em outra assinatura, reinicie o script e selecione para usá-lo.

## <a name="deployment-script-options"></a>Opções de script de implantação

O script usa os seguintes parâmetros:


```
-resourceGroupName
```

Pode ser o nome de um grupo de recursos existente ou um novo.

```
-subscriptionId
```


Opcional, a ID da assinatura na qual os recursos serão implantados.

```
-subscriptionName
```


Ou, como alternativa, o nome da assinatura.

```
-resourceGroupLocation
```


Opcional, um local do grupo de recursos. Se for especificado, tentará criar um novo grupo de recursos neste local.


```
-tenantId
```


Locatário do Azure AD a ser usado. 

```
-development 0|1
```

Opcional, para implantar para desenvolvimento. Use Debug Build e defina o ambiente ASP.Net para desenvolvimento. Crie '. publishsettings ' para importação no Visual Studio 2017 para permitir que ele implante o aplicativo e o serviço diretamente.

```
-onlyBuild 0|1
```

Opcional, para recompilar e reimplantar apenas os aplicativos Web e recriar os contêineres do Docker.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a implantar o cofre OPC do zero, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Gerenciar cofre OPC](howto-opc-vault-manage.md)
