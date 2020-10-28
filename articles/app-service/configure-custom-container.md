---
title: Configure um recipiente personalizado
description: Saiba como configurar um recipiente personalizado no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 9f71efbf7cc606efd598880e90ade3a549402245
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787062"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Configurar um contentor personalizado para o Serviço de Aplicações do Azure

Este artigo mostra-lhe como configurar um recipiente personalizado para funcionar no Azure App Service.

::: zone pivot="container-windows"

Este guia fornece conceitos e instruções fundamentais para a contentorização de aplicações do Windows no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart](quickstart-custom-container.md) e [tutorial](tutorial-custom-container.md) do recipiente personalizado.

::: zone-end

::: zone pivot="container-linux"

Este guia fornece conceitos e instruções fundamentais para a contentorização de aplicações Linux no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [quickstart](quickstart-custom-container.md) e [tutorial](tutorial-custom-container.md) do recipiente personalizado. Há também um quickstart e tutorial [de aplicativo multi-contentores.](quickstart-multi-container.md) [tutorial](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Imagens dos pais apoiadas

Para a sua imagem personalizada do Windows, deve escolher a imagem do pai certo [(imagem base)](https://docs.docker.com/develop/develop-images/baseimages/) para a estrutura que deseja:

- Para implementar aplicações .NET Framework, utilize uma imagem-mãe baseada na versão do Windows Server Core [Servicing Channel (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- Para implementar aplicações .NET Core, utilize uma imagem-mãe baseada na versão semi-anual do Windows Server Nano [Servicing Channel (SAC).](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 

O carregamento das imagens principais durante o arranque da aplicação demora algum tempo. No entanto, pode utilizar uma das seguintes imagens principais que já estão em cache no Serviço de Aplicações do Azure para reduzir o tempo de arranque:

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Alterar a imagem do Docker de um recipiente personalizado

Para alterar uma aplicação de recipiente personalizado existente da imagem atual do Docker para uma nova imagem, utilize o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Use uma imagem de um registo privado

Para utilizar uma imagem de um registo privado, como o Registo do Contentor de Azure, execute o seguinte comando:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Para *\<username>* *\<password>* e, forneça as credenciais de login para a sua conta de registo privado.

## <a name="i-dont-see-the-updated-container"></a>Não vejo o recipiente atualizado.

Se alterar as definições do seu recipiente Docker para apontar para um novo recipiente, poderá demorar alguns minutos até que a aplicação sirva pedidos HTTP do novo recipiente. Enquanto o novo contentor está a ser puxado e iniciado, o Serviço de Aplicações continua a servir pedidos do antigo contentor. Só quando o novo contentor é iniciado e pronto para receber pedidos é que o Serviço de Aplicações começa a enviar-lhe pedidos.

## <a name="how-container-images-are-stored"></a>Como as imagens do contentor são armazenadas

A primeira vez que executou uma imagem personalizada do Docker no Serviço de Aplicações, o App Service faz um `docker pull` e puxa todas as camadas de imagem. Estas camadas são armazenadas em disco, como se estivesses a usar o Docker no local. Cada vez que a aplicação recomeça, o App Service faz um `docker pull` , mas apenas puxa camadas que mudaram. Se não houver alterações, o Serviço de Aplicações utiliza camadas existentes no disco local.

Se a aplicação alterar instâncias de cálculo por qualquer motivo, como aumentar e descer os níveis de preços, o Serviço de Aplicações deve puxar todas as camadas novamente. O mesmo acontece se se escalonar para adicionar instâncias adicionais. Existem também casos raros em que as instâncias da aplicação podem mudar sem uma operação de escala.

## <a name="configure-port-number"></a>Configure o número da porta

Por predefinição, o Serviço de Aplicações assume que o seu recipiente personalizado está a ouvir na porta 80. Se o seu recipiente ouvir uma porta diferente, defina a definição da `WEBSITES_PORT` aplicação na sua aplicação 'Serviço de Aplicações'. Pode defini-lo através da [Cloud Shell.](https://shell.azure.com) Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

O Serviço de Aplicações atualmente permite que o seu recipiente exponha apenas uma porta para pedidos HTTP. 

## <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente

O seu recipiente personalizado pode utilizar variáveis ambientais que precisam de ser fornecidas externamente. Pode passá-los através da [Cloud Shell.](https://shell.azure.com) Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Quando a sua aplicação é executado, as definições da aplicação do Serviço de Aplicações são injetadas no processo como variáveis de ambiente automaticamente. 

::: zone pivot="container-windows"
Para recipientes baseados em IIS ou .NET Framework (4.0 ou superior), são injetados automaticamente nas `System.ConfigurationManager` definições de aplicações .NET e cadeias de ligação pelo Serviço de Aplicações. Para todas as outras línguas ou enquadramento, são fornecidas como variáveis ambientais para o processo, com um dos seguintes prefixos correspondentes:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Este método funciona tanto para aplicações de contentores individuais como para aplicações multi-contentores, onde as variáveis ambientais são especificadas no ficheiro *docker-compose.yml.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Use armazenamento partilhado persistente

::: zone pivot="container-windows"

Pode utilizar o *diretório C:\home* no sistema de ficheiros da sua aplicação para persistir ficheiros em todo o recomeço e partilhá-los em todos os casos. A `C:\home` aplicação na sua aplicação é fornecida para permitir que a sua aplicação de contentores tenha acesso a um armazenamento persistente.

Quando o armazenamento persistente é desativado, os escritos para o `C:\home` diretório não são persistidos. [Os registos de anfitrião do Docker e os registos dos contentores](#access-diagnostic-logs) são guardados num armazenamento partilhado persistente e predefinido que não está ligado ao recipiente. Quando o armazenamento persistente é ativado, todos os escritos para o `C:\home` diretório são persistidos e podem ser acedidos por todas as instâncias de uma aplicação de escala, e o log é acessível em `C:\home\LogFiles` .

Por predefinição, o armazenamento persistente é *desativado* e a definição não está exposta nas Definições de Aplicação. Para o ativar, defina a definição da `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação através da [Cloud Shell](https://shell.azure.com). Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

::: zone-end

::: zone pivot="container-linux"

Pode utilizar o *diretório /home* no sistema de ficheiros da sua aplicação para persistir ficheiros em todo o recomeço e partilhá-los em todos os casos. A `/home` aplicação na sua aplicação é fornecida para permitir que a sua aplicação de contentores tenha acesso a um armazenamento persistente.

Quando o armazenamento persistente é desativado, então escreve para o `/home` diretório não são persistidos em recomeçar apps ou em vários casos. A única exceção é o `/home/LogFiles` diretório, que é usado para armazenar o Docker e os troncos de contentores. Quando o armazenamento persistente é ativado, todos os escritos para o `/home` diretório são persistidos e podem ser acedidos por todos os casos de uma aplicação de escala.

Por predefinição, o armazenamento persistente é *ativado* e a definição não está exposta nas Definições de Aplicação. Para desativá-la, defina a definição da `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação através da [Cloud Shell](https://shell.azure.com). Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=false}
```

::: zone-end

> [!NOTE]
> Também pode [configurar o seu próprio armazenamento persistente.](configure-connect-to-azure-storage.md)

## <a name="detect-https-session"></a>Detetar sessão HTTPS

O Serviço de Aplicações termina o TLS/SSL nas extremidades dianteiras. Isto significa que os pedidos TLS/SSL nunca chegaram à sua aplicação. Não precisa e não deve implementar nenhum suporte para TLS/SSL na sua aplicação. 

As extremidades dianteiras estão localizadas dentro dos centros de dados Azure. Se utilizar o TLS/SSL com a sua aplicação, o seu tráfego através da Internet será sempre encriptado com segurança.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Personalize a injeção de chave de ASP.NET máquina

 Durante o início do recipiente, as teclas geradas automaticamente são injetadas no recipiente como as chaves da máquina para ASP.NET rotinas criptográficas. Pode [encontrar estas chaves no seu recipiente](#connect-to-the-container) procurando as seguintes variáveis ambientais: , . . . . . . . . `MACHINEKEY_Decryption` `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

As novas teclas de cada reinício podem reiniciar ASP.NET forma a autenticação e visualizar o estado, caso a sua aplicação dependa delas. Para evitar a regeneração automática das teclas, [defina-as manualmente como definições de aplicações do Serviço de Aplicações](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Ligue-se ao recipiente

Pode ligar-se diretamente ao seu recipiente Windows para tarefas de diagnóstico navegando para `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Eis como funciona:

- A consola de depurador permite executar comandos interativos, tais como iniciar sessões PowerShell, inspecionar as chaves de registo e navegar em todo o sistema de ficheiros do contentor.
- Funciona separadamente do navegador gráfico acima, que apenas mostra os ficheiros no seu [armazenamento partilhado.](#use-persistent-shared-storage)
- Numa aplicação de escala, a consola de depurado está ligada a uma das instâncias do contentor. Pode selecionar uma instância diferente da entrega de **exemplos** no menu superior.
- Qualquer alteração que faça no recipiente a partir da consola *não* persiste quando a sua aplicação é reiniciada (exceto alterações no armazenamento partilhado), porque não faz parte da imagem do Docker. Para persistir as suas alterações, tais como as definições de registo e a instalação de software, faça-as parte do Dockerfile.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

O Serviço de Aplicações regista as ações do anfitrião Docker, bem como as atividades dentro do contentor. Os registos do anfitrião Docker (registos de plataforma) são enviados por padrão, mas os registos de aplicações ou registos de servidores web a partir do contentor precisam de ser ativados manualmente. Para obter mais informações, consulte [Ativar o registo de aplicações](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) e [ative o registo do servidor web.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

Existem várias formas de aceder aos registos do Docker:

- [No portal Azure](#in-azure-portal)
- [Da consola Kudu](#from-the-kudu-console)
- [Com a API kudu](#with-the-kudu-api)
- [Enviar registos para o monitor Azure](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>No portal Azure

Os registos do Docker são apresentados no portal, na página **de Definições** de Contentores da sua aplicação. Os registos são truncados, mas pode descarregar todos os registos clicando **em Download** . 

### <a name="from-the-kudu-console"></a>Da consola Kudu

Navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` para e clique na pasta **'Ficheiros de Registo'** para ver os ficheiros de registo individuais. Para descarregar todo o **diretório do LogFiles,** clique no ícone **Baixar** à esquerda do nome do diretório. Também pode aceder a esta pasta utilizando um cliente FTP.

No terminal da consola, não é possível aceder à `C:\home\LogFiles` pasta por predefinição, uma vez que o armazenamento partilhado persistente não está ativado. Para ativar este comportamento no terminal da consola, [permita o armazenamento partilhado persistente](#use-persistent-shared-storage).

Se tentar descarregar o registo Docker que está atualmente a ser utilizado através de um cliente FTP, poderá obter um erro por causa de um bloqueio de ficheiro.

### <a name="with-the-kudu-api"></a>Com a API kudu

Navegue diretamente `https://<app-name>.scm.azurewebsites.net/api/logs/docker` para ver metadados para os registos do Docker. Pode ver mais de um ficheiro de registo listado e a `href` propriedade permite-lhe descarregar diretamente o ficheiro de registo. 

Para descarregar todos os registos juntos num ficheiro ZIP, aceda `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Personalizar a memória do recipiente

Por predefinição, todos os contentores do Windows implantados no Serviço de Aplicações Azure estão limitados a 1 GB de RAM. Pode alterar este valor fornecendo a definição da `WEBSITE_MEMORY_LIMIT_MB` aplicação através da [Cloud Shell.](https://shell.azure.com) Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

O valor é definido em MB e deve ser menor e igual à memória física total do hospedeiro. Por exemplo, num plano de Serviço de Aplicações com 8 GB de RAM, o total acumulado de `WEBSITE_MEMORY_LIMIT_MB` todas as aplicações não deve exceder 8 GB. Informações sobre a quantidade de memória disponível para cada nível de preços podem ser encontradas nos preços do [Serviço de Aplicações,](https://azure.microsoft.com/pricing/details/app-service/windows/)na secção **Plano de Recipiente Premium (Windows).**

## <a name="customize-the-number-of-compute-cores"></a>Personalize o número de núcleos computacional

Por padrão, um recipiente Windows funciona com todos os núcleos disponíveis para o seu nível de preços escolhido. É melhor reduzir o número de núcleos que a sua ranhura de paragem utiliza, por exemplo. Para reduzir o número de núcleos utilizados por um recipiente, defina a definição da `WEBSITE_CPU_CORES_LIMIT` aplicação para o número preferido de núcleos. Pode defini-lo através da [Cloud Shell.](https://shell.azure.com) Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> A atualização da definição da aplicação aciona o reinício automático, causando um mínimo de tempo de inatividade. Para uma aplicação de produção, considere trocá-la por uma ranhura de encenação, alterar a definição da aplicação na ranhura de preparação e, em seguida, trocá-la novamente em produção.

Verifique o seu número ajustado indo para a Consola Kudu `https://<app-name>.scm.azurewebsites.net` () e digitando nos seguintes comandos utilizando o PowerShell. Cada comando produz um número.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Os processadores podem ser processadores multicore ou hiper-televisão. Informações sobre quantos núcleos estão disponíveis para cada nível de preços podem ser encontradas nos preços do [Serviço de Aplicações,](https://azure.microsoft.com/pricing/details/app-service/windows/)na secção **Plano de Recipiente Premium (Windows).**

## <a name="customize-health-ping-behavior"></a>Personalizar o comportamento do ping de saúde

O Serviço de Aplicações considera que um recipiente deve ser iniciado com sucesso quando o recipiente começa e responde a um ping HTTP. O pedido de ping de saúde contém o cabeçalho `User-Agent= "App Service Hyper-V Container Availability Check"` . Se o contentor começar mas não responder a um ping após um certo período de tempo, o Serviço de Aplicações regista um evento no diário Docker, dizendo que o recipiente não foi iniciado. 

Se a sua aplicação for intensiva em recursos, o recipiente pode não responder a tempo ao ping HTTP. Para controlar as ações quando os pings HTTP falharem, defina a definição da `CONTAINER_AVAILABILITY_CHECK_MODE` aplicação. Pode defini-lo através da [Cloud Shell.](https://shell.azure.com) Em Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

No PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

A tabela a seguir mostra os valores possíveis:

| Valor | Descrições |
| - | - |
| **Reparação** | Reinicie o recipiente após três verificações consecutivas de disponibilidade |
| **RelatórioOnamente** | O valor predefinido. Não reinicie o contentor, mas apresente-se nos registos do Estivador para o contentor depois de três verificações de disponibilidade consecutivas. |
| **Desligado** | Não verifique se há disponibilidade. |

## <a name="support-for-group-managed-service-accounts"></a>Apoio às Contas de Serviço Geridas pelo Grupo

As contas de serviço geridas pelo grupo (gMSAs) não são atualmente suportadas em contentores Windows no Serviço de Aplicações.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Ativar SSH

O SSH permite a comunicação segura entre um contentor e um cliente. Para que um recipiente personalizado suporte o SSH, deve adicioná-lo ao próprio Dockerfile.

> [!TIP]
> Todos os recipientes Linux incorporados adicionaram as instruções do SSH nos seus repositórios de imagem. Pode consultar as seguintes instruções com o [ repositórioNode.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) para ver como está ativado.

- Utilize as instruções [RUN](https://docs.docker.com/engine/reference/builder/#run) para instalar o servidor SSH e definir a palavra-passe para a conta raiz para `"Docker!"` . Por exemplo, para uma imagem baseada em [Alpine Linux,](https://hub.docker.com/_/alpine)precisa dos seguintes comandos:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Esta configuração não permite ligações externas ao recipiente. O SSH só está disponível `https://<app-name>.scm.azurewebsites.net` através e autenticado com as credenciais de publicação.

- Adicione [este ficheiro sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) ao seu repositório de imagem e utilize a instrução [COPY](https://docs.docker.com/engine/reference/builder/#copy) para copiar o ficheiro para o */etc/ssh/diretório.* Para obter mais informações sobre *sshd_config* ficheiros, consulte [a documentação do OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > O ficheiro *sshd_config* tem de incluir os itens seguintes:
    > - `Ciphers` tem de incluir, pelo menos, um item na lista `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` tem de incluir, pelo menos, um item na lista `hmac-sha1,hmac-sha1-96`.

- Utilize as instruções [EXPOR](https://docs.docker.com/engine/reference/builder/#expose) para abrir a porta 2222 no recipiente. Embora a palavra-passe de raiz seja conhecida, a porta 2222 é inacessível a partir da internet. É acessível apenas por contentores dentro da rede de pontes de uma rede virtual privada.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- No script de arranque do seu recipiente, inicie o servidor SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Por exemplo, veja como o [ recipiente padrãoNode.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) começa o servidor SSH.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configure aplicativos multi-contentores

- [Use armazenamento persistente em Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitações de pré-visualização](#preview-limitations)
- [Opções de Composição de Docker](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Use armazenamento persistente em Docker Compose

Aplicações multi-contentores como o WordPress precisam de armazenamento persistente para funcionar corretamente. Para o ativar, a sua configuração Docker Compose deve indicar um local de armazenamento *fora do* seu contentor. Os locais de armazenamento dentro do seu contentor não persistem alterações para além do reinício da aplicação.

Permitir o armazenamento persistente definindo a definição da `WEBSITES_ENABLE_APP_SERVICE_STORAGE` aplicação, utilizando o comando de configuração de [appsettings az webapp em](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

No seu ficheiro *docker-compose.yml,* mapear a `volumes` opção para `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` é uma variável de ambiente no Serviço de Aplicações, que está mapeado para um armazenamento persistente para a sua aplicação. Por exemplo:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limitações de pré-visualização

O multi-contentor está atualmente em pré-visualização. As seguintes funcionalidades da plataforma do Serviço de Aplicações não são suportadas:

- Autenticação / Autorização
- Identidades geridas
- CORS

### <a name="docker-compose-options"></a>Opções de Composição de Docker

As seguintes listas mostram opções de configuração de Composição de Docker suportadas e não suportadas:

#### <a name="supported-options"></a>Opções suportadas

- command
- entrypoint
- ambiente
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opções não suportadas

- build (não é permitida)
- depends_on (ignorada)
- networks (ignorada)
- secrets (ignorada)
- portas que não 80 e 8080 (ignoradas)

> [!NOTE]
> Quaisquer outras opções não explicitamente chamadas são ignoradas na Visualização Pública.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Migrar software personalizado para o Azure App Service usando um recipiente personalizado](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Tutorial: App WordPress multi-contentor](tutorial-multi-container-app.md)

::: zone-end

Ou, consulte recursos adicionais:

[Certificado de carga em recipientes Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
