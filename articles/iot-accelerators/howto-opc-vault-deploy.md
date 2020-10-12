---
title: Como implantar o serviço de gestão de certificados OPC Vault - Azure ! Microsoft Docs
description: Como implantar o serviço de gestão de certificados OPC Vault do zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 365cb0912d84f0664b2a5432cd54cd553df62466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282028"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Construa e implemente o serviço de gestão de certificados OPC Vault

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como implantar o serviço de gestão de certificados OPC Vault em Azure.

> [!NOTE]
> Para mais informações, consulte o [repositório](https://github.com/Azure/azure-iiot-opc-vault-service)GitHub OPC Vault .

## <a name="prerequisites"></a>Pré-requisitos

### <a name="install-required-software"></a>Instalar o software necessário

Atualmente, a operação de construção e implantação está limitada ao Windows.
As amostras estão todas escritas para C# .NET Standard, que precisa de construir o serviço e amostras para implantação.
Todas as ferramentas necessárias para .NET Standard vêm com as ferramentas .NET Core. Ver [Começar com .NET Core](https://docs.microsoft.com/dotnet/articles/core/getting-started).

1. [Instalar .NET Core 2.1+][dotnet-install].
2. [Instale o Docker][docker-url] (opcional, apenas se for necessária a construção local do Docker).
4. Instale as [ferramentas da linha de comando Azure para o PowerShell][powershell-install].
5. Inscreva-se para uma [subscrição da Azure.][azure-free]

### <a name="clone-the-repository"></a>Clonar o repositório

Se ainda não o fizeste, clone este repositório do GitHub. Abra uma solicitação de comando ou terminal e execute o seguinte:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

Em alternativa, pode clonar o repo diretamente no Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Construa e implemente o serviço Azure no Windows

Um script PowerShell fornece uma maneira fácil de implementar o microservice OPC Vault e a aplicação.

1. Abra uma janela PowerShell na raiz do repo. 
3. Vá para a pasta de implantação `cd deploy` .
3. Escolha um nome para `myResourceGroup` o que é improvável que cause um conflito com outras páginas web implementadas. Consulte a secção "Nome do site já em uso" mais tarde neste artigo.
5. Inicie a implantação para `.\deploy.ps1` instalação interativa ou introduza uma linha de comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se planeia desenvolver-se com esta implementação, adicione `-development 1` para ativar a UI Swagger e implementar construções de depuração.
6. Siga as instruções no script para iniciar sôm na sua subscrição e para fornecer informações adicionais.
9. Após uma operação bem sucedida de construção e implantação, deverá ver a seguinte mensagem:
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
   > Em caso de problemas, consulte a secção "Falhas de resolução de problemas" mais tarde no artigo.

8. Abra o seu navegador favorito e abra a página da aplicação: `https://myResourceGroup.azurewebsites.net`
8. Dê à aplicação web e ao microserviço OPC Vault alguns minutos para aquecer após a implementação. A página inicial da web pode parar de responder na primeira utilização, por um minuto, até obter as primeiras respostas.
11. Para dar uma olhada na API de Swagger, abra: `https://myResourceGroup-service.azurewebsites.net`
13. Para iniciar um servidor GDS local com dotnet, comece `.\myResourceGroup-gds.cmd` . Com o Docker, `.\myResourceGroup-dockergds.cmd` começa.

É possível recolocar uma construção com as mesmas definições. Esteja ciente de que tal operação renova todos os segredos da aplicação, e pode redefinir algumas configurações nos registos de candidaturas do Azure Ative (Azure AD).

Também é possível recolocar apenas os binários de aplicações web. Com o `-onlyBuild 1` parâmetro, novos pacotes zip do serviço e a aplicação são implantados nas aplicações web.

Após uma implementação bem sucedida, pode começar a utilizar os serviços. Consulte [gerir o serviço de gestão de certificados OPC Vault](howto-opc-vault-manage.md).

## <a name="delete-the-services-from-the-subscription"></a>Eliminar os serviços da subscrição

Eis como:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá ao grupo de recursos em que o serviço foi implantado.
3. Selecione **Eliminar grupo de recursos** e confirme.
4. Após um curto período de tempo, todos os componentes de serviço implantados são eliminados.
5. Aceda às inscrições da **Azure Ative Directory**  >  **App**.
6. Devem ser listados três registos para cada grupo de recursos implantados. As inscrições têm os seguintes nomes: `resourcegroup-client` `resourcegroup-module` , . `resourcegroup-service` . Elimine cada registo separadamente.

Agora todos os componentes implantados são removidos.

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de resolução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Utilize um nome de grupo de recursos curto e simples. O nome também é usado para nomear recursos e o prefixo URL de serviço. Como tal, deve cumprir os requisitos de nomeação de recursos.  

### <a name="website-name-already-in-use"></a>Nome do site já em uso

É possível que o nome do site já esteja em uso. Tens de usar um nome de grupo de recursos diferente. Os nomes de anfitriões em uso pelo script de implantação são: https: \/ /resourcegroupname.azurewebsites.net e https: \/ /resourgroupname-service.azurewebsites.net.
Outros nomes de serviços são construídos pela combinação de hashes de nome curto, e são improváveis de entrar em conflito com outros serviços.

### <a name="azure-ad-registration"></a>Registo do Azure AD 

O script de implementação tenta registar três aplicações AD Azure em Azure AD. Dependendo das suas permissões no inquilino Azure AD selecionado, esta operação pode falhar. Existem duas opções:

- Se escolheu um inquilino AZure AD de uma lista de inquilinos, reinicie o roteiro e escolha um diferente da lista.
- Em alternativa, desloque um inquilino privado da Azure AD em outra subscrição. Reinicie o script e selecione para usá-lo.

## <a name="deployment-script-options"></a>Opções de script de implementação

O script toma os seguintes parâmetros:


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


Este é o inquilino da AD AZure para usar. 

```
-development 0|1
```

Isto é para ser implementado para o desenvolvimento. Use a construção de depurg, e coloque o ambiente ASP.NET para o desenvolvimento. Criar `.publishsettings` para importação no Visual Studio 2017, para permitir-lhe implementar a app e o serviço diretamente. Este parâmetro também é opcional.

```
-onlyBuild 0|1
```

Isto é para reconstruir e redistribuir apenas as aplicações web, e para reconstruir os contentores Docker. Este parâmetro também é opcional.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://www.microsoft.com/net/learn/get-started

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar o Cofre OPC do zero, pode:

> [!div class="nextstepaction"]
> [Gerir o Cofre OPC](howto-opc-vault-manage.md)
