---
title: Como implementar o serviço de gestão de certificados OPC Vault - Azure [ Microsoft Docs
description: Como implantar o serviço de gestão de certificados OPC Vault do zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 7ee186684b702a42335c6e1a7832cc5c761a69d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686946"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Construir e implementar o serviço de gestão de certificados OPC Vault

Este artigo explica como implementar o serviço de gestão de certificados OPC Vault em Azure.

> [!NOTE]
> Para mais informações, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-vault-service)gitHub OPC Vault .

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-required-software"></a>Instalar software necessário

Atualmente, a operação de construção e implementação está limitada ao Windows.
As amostras estão todas escritas para c# .NET Standard, que você precisa para construir o serviço e amostras para implantação.
Todas as ferramentas necessárias para o .NET Standard vêm com as ferramentas .NET Core. Ver [Começar com .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Instalar .NET Core 2.1+][dotnet-install].
2. [Instale o Docker][docker-url] (opcional, apenas se for necessária a construção local do Docker).
4. Instale as [ferramentas de linha de comando Azure para powerShell][powershell-install].
5. Inscreva-se para uma [subscrição Azure.][azure-free]

### <a name="clone-the-repository"></a>Clonar o repositório

Se ainda não o fez, clone este repositório GitHub. Abra um pedido de comando ou terminal e execute o seguinte:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Em alternativa, pode clonar o repo diretamente no Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Construir e implementar o serviço Azure no Windows

Um script PowerShell fornece uma maneira fácil de implementar o microserviço OPC Vault e a aplicação.

1. Abra uma janela PowerShell na raiz do repo. 
3. Vá para a `cd deploy`pasta de implantação .
3. Escolha um `myResourceGroup` nome para que seja improvável causar um conflito com outras páginas web implantadas. Consulte a secção "Nome do Site já em uso" mais tarde neste artigo.
5. Inicie a `.\deploy.ps1` implantação para instalação interativa ou entre numa linha de comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se planeia desenvolver-se com `-development 1` esta implementação, adicione para permitir a UI Swagger e implemente as construções de depuração.
6. Siga as instruções do script para iniciar sessão na sua subscrição e para fornecer informações adicionais.
9. Após uma operação de construção e implantação bem sucedida, deve ver a seguinte mensagem:
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

   > [!NOTE]
   > Em caso de problemas, consulte a secção "Falhas de implantação de problemas" mais tarde no artigo.

8. Abra o seu navegador favorito e abra a página de aplicações:`https://myResourceGroup.azurewebsites.net`
8. Dê à aplicação web e ao microserviço OPC Vault alguns minutos para aquecer após a implementação. A página inicial da web pode aguentar-se na primeira utilização, durante um minuto, até obter as primeiras respostas.
11. Para dar uma olhada na API swagger, aberto:`https://myResourceGroup-service.azurewebsites.net`
13. Para iniciar um servidor GDS local `.\myResourceGroup-gds.cmd`com dotnet, inicie . Com o `.\myResourceGroup-dockergds.cmd`Docker, começa.

É possível reimplantar uma construção com as mesmas configurações. Esteja ciente de que tal operação renova todos os segredos de aplicação, podendo redefinir algumas configurações nos registos de aplicação do Azure Ative Directory (Azure AD).

Também é possível reimplantar apenas os binários da aplicação web. Com o `-onlyBuild 1`parâmetro, novos pacotes zip do serviço e a aplicação são implantados para as aplicações web.

Após uma implementação bem sucedida, pode começar a utilizar os serviços. Ver Gerir o serviço de [gestão de certificados OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Eliminar os serviços da subscrição

Eis como:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá ao grupo de recursos em que o serviço foi implantado.
3. Selecione **Eliminar grupo de recursos** e confirme.
4. Após um curto período de tempo, todos os componentes de serviço implantados são eliminados.
5. Vá às > **inscrições**da App de **Diretório Ativo Azure.**
6. Deve haver três registos listados para cada grupo de recursos implantados. As inscrições têm os `resourcegroup-client` `resourcegroup-module`seguintes nomes: , . `resourcegroup-service` Elimine cada registo separadamente.

Agora todos os componentes implantados são removidos.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implementação de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Use um nome de grupo de recursos curto e simples. O nome também é usado para nomear recursos e o prefixo URL de serviço. Como tal, deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso. Tens de usar um nome de grupo de recursos diferente. Os nomes de anfitriões utilizados\/pelo script de\/implementação são: https: /resourcegroupname.azurewebsites.net e https: /resourgroupname-service.azurewebsites.net.
Outros nomes de serviços são construídos pela combinação de hashes de nome curto, e são improváveis de entrar em conflito com outros serviços.

### <a name="azure-ad-registration"></a>Registo do Azure AD 

O script de implantação tenta registar três aplicações Azure AD em Azure AD. Dependendo das suas permissões no inquilino da AD Azure selecionado, esta operação pode falhar. Existem duas opções:

- Se escolheu um inquilino da AD Azure de uma lista de inquilinos, reinicie o guião e escolha um diferente da lista.
- Em alternativa, desloque um inquilino azure privado numa outra subscrição. Reinicie o script e selecione para usá-lo.

## <a name="deployment-script-options"></a>Opções de script de implementação

O guião tem os seguintes parâmetros:


```
-resourceGroupName
```

Este pode ser o nome de um grupo de recursos existente ou novo.

```
-subscriptionId
```


Este é o ID de subscrição onde os recursos serão implantados. É opcional.

```
-subscriptionName
```


Em alternativa, pode utilizar o nome de subscrição.

```
-resourceGroupLocation
```


Esta é uma localização de grupo de recursos. Se especificado, este parâmetro tenta criar um novo grupo de recursos neste local. Este parâmetro também é opcional.


```
-tenantId
```


Este é o inquilino da AD Azure para usar. 

```
-development 0|1
```

Isto é para o desenvolvimento. Use a construção de depuração e coloque o ambiente ASP.NET para o Desenvolvimento. Criar `.publishsettings` para importação no Visual Studio 2017, para permitir a sua implementação direta da app e do serviço. Este parâmetro também é opcional.

```
-onlyBuild 0|1
```

Isto é para reconstruir e reimplantar apenas as aplicações web, e para reconstruir os contentores Docker. Este parâmetro também é opcional.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar o Cofre OPC do zero, pode:

> [!div class="nextstepaction"]
> [Gerir o Cofre OPC](howto-opc-vault-manage.md)
