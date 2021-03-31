---
title: Resolução de problemas Azure SQL Edge implantações
description: Saiba mais sobre possíveis erros ao implementar a Azure SQL Edge
keywords: SQL Edge, resolução de problemas, erros de implementação
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333108"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Resolução de problemas Azure SQL Edge implantações 

Este artigo fornece informações sobre possíveis erros vistos ao implementar e utilizar recipientes Azure SQL Edge, e fornece técnicas de resolução de problemas para ajudar a resolver estes problemas. 

A Azure SQL Edge suporta dois modelos de implantação: 
- Implantação conectada através do Azure IoT Edge: Azure SQL Edge está disponível no Azure Marketplace e pode ser implantado como um módulo para [Azure IoT Edge](../iot-edge/about-iot-edge.md). Para mais informações, consulte [Deploy Azure SQL Edge](deploy-portal.md).<br>

- Implantação desligada: As imagens do contentor Azure SQL Edge podem ser retiradas do estivador e implantadas quer como um recipiente de estiva autónomo, quer num aglomerado de kubernetes. Para obter mais informações, consulte [Implementar Azure SQL Edge com Docker](disconnected-deployment.md) e implementar um recipiente [Azure SQL Edge em Kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Dispositivo e implementações IoT Edge de resolução de problemas

Se tiver erros ao implementar SQL Edge através do Azure IoT Edge, certifique-se de que o `iotedge` serviço está corretamente configurado e em funcionamento. Os seguintes documentos podem ser úteis quando se resolvam problemas relacionados com a Azure IoT Edge:
- [Questões e resoluções comuns para Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Resolução de problemas do seu dispositivo IoT Edge](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Erros de comando do Docker

Se tiver erros para qualquer `docker` comando, certifique-se de que o serviço de estivador está em funcionamento e tente executar com permissões elevadas.

Por exemplo, no Linux, poderá obter o seguinte erro ao executar `docker` comandos:

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Se tiver este erro no Linux, tente executar os mesmos comandos pré-afaturou-se com `sudo` . Se isso falhar, verifique se o serviço de estivador está a funcionar e inicie-o se necessário.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

No Windows, verifique se está a lançar o PowerShell ou o seu pedido de comando como Administrador.

## <a name="azure-sql-edge-container-startup-errors"></a>Erros de arranque do contentor Azure SQL Edge

Se o recipiente SQL Edge não funcionar, experimente os seguintes testes:

- Se estiver a utilizar o Azure IoT Edge, certifique-se de que as imagens do módulo foram descarregadas com sucesso e que as variáveis ambientais e as opções de criação de contentores estão corretamente especificadas no manifesto do módulo.

- Se estiver a utilizar a implantação baseada em estivadores ou kubernetes, certifique-se de que o `docker run` comando está corretamente formado. Para obter mais informações, consulte [implementar a borda sql do Azure com o Docker](disconnected-deployment.md) e implementar um recipiente [Azure SQL Edge em Kubernetes](deploy-kubernetes.md).

- Se tiver um erro `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` como, está a tentar mapear a porta de contentores 1433 para uma porta que já está a ser utilizada. Isto pode acontecer se estiver a executar SQL Edge localmente na máquina hospedeira. Também pode acontecer se iniciar dois recipientes SQL Edge e tentar mapear ambos para a mesma porta hospedeira. Se isso acontecer, utilize o `-p` parâmetro para mapear a porta de contentores 1433 para uma porta hospedeira diferente. Por exemplo: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Se tiver um erro como `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` quando tentar iniciar um recipiente, adicione o seu utilizador ao grupo de estivadores em Ubuntu. Em seguida, o logout e o login novamente, uma vez que esta alteração irá afetar novas sessões. 

   ```bash
    usermod -aG docker $USER
   ```

- Verifique se há alguma mensagem de erro do recipiente.

   ```bash
   docker logs e69e056c702d
   ```

- Se estiver a utilizar qualquer software de gestão de contentores, certifique-se de que suporta processos de contentores funcionando como raiz. O processo sqlservr no recipiente funciona como raiz.

- Por defeito, os recipientes Azure SQL Edge funcionam como um utilizador sem raízes chamado `mssql` . Se estiver a utilizar pontos de montagem ou volumes de dados para persistir dados, certifique-se de que o `mssql` utilizador tem permissões adequadas sobre o volume. Para obter mais informações, consulte [Run como utilizador não raiz](configure.md#run-azure-sql-edge-as-non-root-user) e [Pereça Dados](configure.md#persist-your-data).

- Se o seu contentor SQL Edge Docker sair imediatamente após o início, verifique os seus registos de estivadores. Se estiver a utilizar o PowerShell no Windows com o `docker run` comando, utilize aspas duplas em vez de cotações individuais. Com o PowerShell Core, utilize aspas individuais.

- Reveja os [registos de erro sql Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Falhas de ligação SQL Edge

Se não conseguir ligar-se à instância SQL Edge que funciona no seu contentor, experimente os seguintes testes:

- Certifique-se de que o seu recipiente SQL Edge está a funcionar olhando para a coluna **STATUS** da `docker ps -a` saída. Se não, use `docker start <Container ID>` para ligá-lo.

- Se mapear para uma porta hospedeira não padrão (não 1433), certifique-se de que está a especificar a porta na sua cadeia de ligação. Pode ver o mapeamento da porta na coluna **PORTS** da `docker ps -a` saída. Para obter mais informações sobre a ligação ao Aresta SQL Azure, consulte [Connect and consulta Azure SQL Edge](connect.md)

- Se já tinha implantado o SQL Edge com volume de dados mapeado ou um recipiente de volume de dados, e agora está a utilizar o volume de dados ou o recipiente de volume de dados mapeado existente, o SQL Edge ignora o valor da `MSSQL_SA_PASSWORD` variável ambiental. Em vez disso, é utilizada a palavra-passe do utilizador SA previamente configurada. Isto acontece porque o SQL Edge reutiliza os ficheiros de bases de dados principais existentes no volume mapeado ou no recipiente de volume de dados. Se encontrar este problema, pode utilizar as seguintes opções:

    - Conecte-se utilizando a palavra-passe anteriormente utilizada, se ainda estiver disponível.
    - Configure a Borda SQL para utilizar um volume de volume ou volume de dados diferente.
    - Remova os ficheiros de base de dados principais existentes (master.mdf e mastlog.mdf) do volume mapeado ou do recipiente de volume de dados.

- Reveja os [registos de erro sql Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Registos de configuração e erro SQL Edge

Por predefinição, os registos de erro sql Edge estão presentes no diretório **/var/opt/mssql/log** dentro do contentor e podem ser acedidos utilizando qualquer uma das seguintes formas:

- Se montou um diretório de anfitriões para **/var/opt/mssql** quando criou o seu recipiente, pode, em vez disso, olhar para o subdiretório de **log** no caminho mapeado no hospedeiro.
- Utilizando um pedido de comando interativo para ligar ao recipiente. Se o recipiente não estiver a funcionar, ligue primeiro o recipiente. Em seguida, utilize um pedido de comando interativo para inspecionar os registos. Pode obter a identificação do contentor executando o `docker ps` comando.

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    A partir da sessão de festa dentro do seu recipiente, executar os seguintes comandos:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Se o recipiente SQL Edge estiver a funcionar e conseguir ligar-se ao caso utilizando ferramentas do cliente, então pode utilizar o procedimento armazenado `sp_readerrorlog` para ler o conteúdo do registo de erro sql Edge.

## <a name="execute-commands-in-a-container"></a>Execute comandos num contentor

Se tiver um recipiente em funcionamento, pode executar comandos dentro do recipiente a partir de um terminal de hospedeiro.

Para obter a identificação do contentor:

```bash
docker ps -a
```

Para iniciar um terminal de bash no funcionado do contentor:

```bash
docker exec -it <Container ID> /bin/bash
```

Agora pode executar comandos como se estivesse a executá-los no terminal dentro do contentor. Quando terminar, `exit` escreva. Isto sai na sessão de comando interativo, mas o seu contentor continua a funcionar.

### <a name="enabling-verbose-logging"></a>Habilitação de registo verboso

Se o nível de registo predefinido para o motor de streaming não fornecer informações suficientes, a depuração do motor de streaming pode ser ativada em SQL Edge. Para ativar a extração de depuração adicione a `RuntimeLogLevel=debug` variável ambiente à sua implementação SQL Edge. Depois de permitir a depuração do registo, tente reproduzir o problema e verifique se os registos são de mensagens ou exceções relevantes. 

> [!NOTE]
> A opção Verbose Logging deve ser utilizada apenas para a resolução de problemas e não para a carga de trabalho regular de produção. 


## <a name="next-steps"></a>Passos seguintes

- [Machine Learning e Inteligência Artificial com ONNX em SQL Edge](onnx-overview.md)
- [Streaming de dados em Azure SQL Edge](stream-data.md)
- [Retenção e limpeza de dados](data-retention-overview.md)
- [Preencher lacunas de tempo e impurção de valores em falta](imputing-missing-values.md)







