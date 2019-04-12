---
title: Como implementar o módulo de cofre OPC do zero - Azure | Documentos da Microsoft
description: Como implementar OPC cofre a partir do zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496901"
---
# <a name="deploy-opc-vault-from-scratch"></a>Implementar o Cofre OPC de raiz

O Cofre de OPC é um microsserviço que pode configurar, registe-se e gerenciar o ciclo de vida do certificado para o servidor OPC UA e aplicações de cliente na cloud. Este artigo mostra-lhe como implementar OPC cofre a partir do zero.

## <a name="configuration-and-environment-variables"></a>Variáveis de ambiente e de configuração

A configuração de serviço é armazenada com adaptadores de configuração do ASP.NET Core, no appsettings.ini. O formato INI permite armazenar valores num formato legível, com comentários.
O aplicativo também suporta a inserção de variáveis de ambiente, como credenciais e detalhes de rede. (Esta secção originalmente é intitulada variáveis de ambiente e de configuração de TODO).

O ficheiro de configuração no repositório faz referência a algumas variáveis de ambiente que precisam de ser criado, pelo menos, uma vez. Dependendo do seu sistema operacional e o IDE, existem várias formas para gerir as variáveis de ambiente:

- Para os utilizadores do Windows, o script de env-vars-Setup tem de ser preparada e executada apenas uma vez. Quando executada, as definições serão mantidas entre sessões de terminal e reinicializações.

- Para ambientes Linux e no OSX, o script de configuração do vars env precisa ser executado sempre que uma nova consola é aberta. Dependendo do sistema operacional e do terminal, existem formas para manter valores globalmente, para obter mais informações, que essas páginas devem ajudar:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Variáveis de ambiente pode ser definida também a partir do Visual Studio, nas propriedades do projeto, no painel esquerdo, selecione "Propriedades de configuração" e "Ambiente" para obter a uma secção onde pode adicionar várias variáveis.

- IntelliJ Rider: Variáveis de ambiente podem ser definidas em cada configuração de Run, da mesma forma para o IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Executar e depurar com o Visual Studio

Visual Studio permite-lhe rapidamente, abra a aplicação sem usar uma linha de comandos, sem configurar nada fora do IDE.

Passos a utilizar o Visual Studio 2017:

1. Abra a solução usando o `iot-opc-gds-service.sln` ficheiro.

1. Quando a solução é carregada, faça duplo clique no `WebService` do projeto, selecione e vá para o `Debug` secção.

1. Na mesma seção, defina as variáveis de ambiente necessárias.

1. Prima **F5**, ou o **executar** ícone. Visual Studio deve abrir o browser que mostra o estado do serviço no formato JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Executar e depurar com o IntelliJ Rider

1. Abra a solução usando o `iot-opc-gds-service.sln` ficheiro.

1. Quando a solução é carregada, aceda a `Run > Edit Configurations` e crie um novo `.NET Project` configuração.

1. Na configuração, selecione o projeto WebService.

1. Guardar as definições e execute a configuração de criada a partir da barra de ferramentas do IDE.

1. Deverá ver o serviço de inicializar as mensagens na janela da execução do IntelliJ com detalhes como a URL onde o serviço web está em execução e o serviço de registos.

## <a name="build-and-run-from-the-command-line"></a>Criar e executar a partir da linha de comandos

A pasta de scripts contém alguns scripts para as tarefas frequentes:

- `build`: Todos os projetos de compilar e executar os testes.

- `compile`: Compile todos os projetos.

- `run`: Os projetos de compilar e executar o serviço, que solicita privilégios elevados no Windows para executar o serviço web.

Verifique os scripts para a configuração de variáveis de ambiente. Pode definir as variáveis de ambiente globalmente no seu sistema operacional, ou utilize o script de "env-vars-setup" na pasta de scripts.

### <a name="sandbox"></a>Sandbox

Os scripts partem do princípio de que configurou o ambiente de desenvolvimento com .NET Core e a Docker. Pode evitar a instalação de núcleo de .NET e instalar apenas o Docker e utilize o parâmetro da linha de comandos `--in-sandbox` (ou a forma `-s`), por exemplo:

- `build --in-sandbox`: Executa a tarefa de compilação dentro de um contentor do Docker (krátká forma `build -s`).

- `compile --in-sandbox`: Executa a tarefa de compilação dentro de um contentor do Docker (krátká forma `compile -s`).

- `run --in-sandbox`: Inicia o serviço dentro de um contentor do Docker (krátká forma `run -s`).

As imagens do Docker utilizadas para a área de segurança estão alojadas no Docker Hub [aqui](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Empacotar a aplicação para uma imagem do Docker

O `scripts` pasta inclui uma subpasta do docker com os arquivos necessários para criar o pacote de serviço para uma imagem do Docker:

- `Dockerfile`: Especificações de imagens do docker.
- `build`: Crie um contentor do Docker e armazenar a imagem no registo local.
- `run`: Execute o contentor do Docker a partir da imagem armazenada no registo local.
- `content`: Uma pasta com os arquivos copiados para a imagem, incluindo o script do ponto de entrada.

## <a name="azure-iot-hub-setup"></a>Configuração do IoT Hub do Azure

Para utilizar os microsserviços, configure o seu IoT Hub do Azure para testes de desenvolvimento e integração.

O projeto inclui alguns scripts de Bash para ajudá-lo com esta configuração:

- Crie novo IoT Hub: `./scripts/iothub/create-hub.sh`

- Liste os hubs existentes: `./scripts/iothub/list-hubs.sh`

- Mostra o IoT Hub detalhes (por exemplo, chaves): `./scripts/iothub/show-hub.sh`

E caso tivesse várias subscrições do Azure:

- Mostra lista de subscrições: `./scripts/iothub/list-subscriptions.sh`

- Alterar a subscrição atual: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Configuração de desenvolvimento

### <a name="net-setup"></a>Configuração do .NET

O fluxo de trabalho do projeto é gerido através de [.NET Core](https://dotnet.github.io) 1.x, que tem de instalar no seu ambiente, para que possa executar todos os scripts e certifique-se de que o seu IDE funciona conforme esperado.

Também fornecemos uma [versão Java](https://github.com/Azure/iot-opc-gds-service-dotnet) deste projeto e outros componentes de PCS de IoT do Azure.

### <a name="ide"></a>IDE

Aqui estão alguns dos IDEs de que pode usar para trabalhar em PCS de IoT do Azure:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Configuração do Git

O projeto inclui um hook do Git, para automatizar algumas verificações antes de aceitar uma alteração de código. Pode executar os testes manualmente ou permitir que a plataforma de CI para executar os testes. Usamos o hook do Git seguinte para executar todos os testes antes de enviar as alterações de código para o GitHub e acelerar o fluxo de trabalho de desenvolvimento automaticamente.

Se a qualquer momento que pretende remover o hook, basta eliminar o ficheiro instalado em `.git/hooks`. Também pode ignorar o hook de pré-consolidação de usando o `--no-verify` opção.

#### <a name="pre-commit-hook-with-sandbox"></a>Hook de pré-consolidação de com a sandbox

Para configurar os ganchos incluídos, abra a consola do Windows/Linux/MacOS e execute:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Com esta configuração, quando a verificação em arquivos, o Git verifica que o aplicativo passar todos os testes, executados a compilação e os testes de dentro de um contentor de Docker que está configurado com todos os requisitos de desenvolvimento.

#### <a name="pre-commit-hook-without-sandbox"></a>Hook de pré-consolidação de sem proteção de segurança

> [!NOTE] 
> Requer o hook sem sandbox [.NET Core](https://dotnet.github.io) no sistema de caminho.

Para configurar os ganchos incluídos, abra a consola do Windows/Linux/MacOS e execute:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
Com esta configuração, quando a verificação em arquivos, o Git verifica que o aplicativo passar todos os testes, executados a compilação e os testes em sua estação de trabalho usando as ferramentas instaladas no seu sistema operacional.

Documentação de orientação sobre o estilo de código do projeto:

- Onde razoável, o comprimento de linhas está limitado a 80 carateres máx. para ajudar a revisões de código e editores de linha de comandos.

- Avanço de blocos de código com quatro espaços. O caractere separador deve ser evitado.

- Os arquivos de texto usam final de Unix do formato de linha (LF).

- Injeção de dependência é gerenciada com o [Autofac](https://autofac.org).

- Campos APIs de serviço Web são CamelCased, exceto de metadados.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar OPC cofre a partir do zero, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Implementar OPC duplo a partir do zero](howto-opc-twin-deploy-modules.md)