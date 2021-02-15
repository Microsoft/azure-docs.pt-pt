---
title: Criar um integration runtime autoalojado
description: Saiba como criar um tempo de integração auto-hospedado na Azure Data Factory, que permite às fábricas de dados aceder às lojas de dados numa rede privada.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 02/10/2021
ms.openlocfilehash: 3e61b6a0f17d2d21aaaebc5ff42b0221cf851a4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389515"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Create and configure a self-hosted integration runtime (Criar e configurar um runtime de integração autoalojado)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O tempo de integração (IR) é a infraestrutura de computação que a Azure Data Factory utiliza para fornecer capacidades de integração de dados em diferentes ambientes de rede. Para mais detalhes sobre o IR, consulte [a visão geral do tempo de execução da Integração](concepts-integration-runtime.md).

Um tempo de integração auto-hospedado pode executar atividades de cópia entre uma loja de dados em nuvem e uma loja de dados em uma rede privada. Também pode despachar atividades de transformação contra recursos computacional numa rede no local ou numa rede virtual Azure. A instalação de um tempo de integração auto-hospedado necessita de uma máquina no local ou de uma máquina virtual dentro de uma rede privada.  

Este artigo descreve como pode criar e configurar um IR auto-hospedado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>Considerações para a utilização de um IR auto-hospedado

- Você pode usar um único tempo de integração auto-hospedado para várias fontes de dados no local. Você também pode partilhá-lo com outra fábrica de dados dentro do mesmo inquilino Azure Ative Directory (Azure AD). Para obter mais informações, consulte [partilhar um tempo de integração auto-hospedado.](./create-shared-self-hosted-integration-runtime-powershell.md)
- Pode instalar apenas uma instância de um tempo de integração auto-hospedado em qualquer máquina. Se tiver duas fábricas de dados que precisam de aceder a fontes de dados no local, utilize a [funcionalidade de partilha de IR auto-hospedada](./create-shared-self-hosted-integration-runtime-powershell.md) para partilhar o IR auto-hospedado, ou instale o IR auto-hospedado em dois computadores no local, um para cada fábrica de dados.  
- O tempo de integração auto-hospedado não precisa de estar na mesma máquina que a fonte de dados. No entanto, ter o tempo de integração auto-hospedado perto da fonte de dados reduz o tempo de tempo de integração auto-hospedado para se ligar à fonte de dados. Recomendamos que instale o tempo de funcionamento de integração auto-hospedado numa máquina que difere da que acolhe a fonte de dados no local. Quando o tempo de integração auto-hospedado e a fonte de dados estão em diferentes máquinas, o tempo de integração auto-hospedado não compete com a fonte de dados para recursos.
- Pode ter vários tempos de integração auto-hospedados em diferentes máquinas que se ligam à mesma fonte de dados no local. Por exemplo, se tiver dois tempos de integração auto-hospedados que servem duas fábricas de dados, a mesma fonte de dados no local pode ser registada em ambas as fábricas de dados.
- Utilize um tempo de integração auto-hospedado para suportar a integração de dados dentro de uma rede virtual Azure.
- Trate a sua fonte de dados como uma fonte de dados no local que está por trás de uma firewall, mesmo quando utiliza o Azure ExpressRoute. Utilize o tempo de funcionação da integração auto-hospedada para ligar o serviço à fonte de dados.
- Utilize o tempo de integração auto-hospedado mesmo que a loja de dados esteja na nuvem de uma infraestrutura Azure como máquina virtual de Serviço (IaaS).
- As tarefas podem falhar num tempo de integração auto-hospedado que instalou num servidor Windows para o qual a encriptação compatível com FIPS está ativada. Para resolver este problema, tem duas opções: armazenar credenciais/valores secretos num Cofre de Chaves Azure ou desativar a encriptação compatível com FIPS no servidor. Para desativar a encriptação compatível com o FIPS, altere o valor do sub-chave do registo seguinte de 1 (ativado) para 0 (desativado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` . Se utilizar o [tempo de integração auto-hospedado como proxy para o tempo de execução da integração SSIS,](./self-hosted-integration-runtime-proxy-ssis.md)a encriptação compatível com FIPS pode ser ativada e será usada ao mover dados de instalações para Azure Blob Storage como uma área de preparação.

## <a name="command-flow-and-data-flow"></a>Fluxo de comando e fluxo de dados

Quando move dados entre as instalações e a nuvem, a atividade utiliza um tempo de integração auto-hospedado para transferir os dados entre uma fonte de dados no local e a nuvem.

Aqui está um resumo de alto nível dos passos de fluxo de dados para copiar com um IR auto-hospedado:

![A visão geral de alto nível do fluxo de dados](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Um desenvolvedor de dados cria um tempo de integração auto-hospedado dentro de uma fábrica de dados Azure, utilizando o portal Azure ou o cmdlet PowerShell.

2. O desenvolvedor de dados cria um serviço ligado para uma loja de dados no local. O desenvolvedor fá-lo especificando a instância de execução de integração auto-hospedada que o serviço deve usar para se conectar às lojas de dados.

3. O nó de tempo de execução de integração auto-alojado encripta as credenciais utilizando a Interface de Programação da Aplicação de Proteção de Dados do Windows (DPAPI) e guarda as credenciais localmente. Se vários nós estiverem definidos para uma alta disponibilidade, as credenciais são mais sincronizadas em outros nós. Cada nó encripta as credenciais utilizando o DPAPI e armazena-as localmente. A sincronização credencial é transparente para o desenvolvedor de dados e é tratada pelo IR auto-hospedado.

4. A Azure Data Factory comunica com o tempo de integração auto-hospedado para agendar e gerir postos de trabalho. A comunicação é através de um canal de controlo que utiliza uma ligação [Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) partilhada. Quando um trabalho de atividade precisa ser executado, data factory faz fila do pedido juntamente com qualquer informação credencial. Fá-lo caso as credenciais não estejam já armazenadas no tempo de integração auto-hospedado. O tempo de integração auto-organizado começa o trabalho depois de sondar a fila.

5. O tempo de execução de integração auto-alojado copia dados entre uma loja no local e armazenamento em nuvem. A direção da cópia depende da configuração da atividade da cópia no pipeline de dados. Para este passo, o tempo de integração auto-hospedado comunica diretamente com serviços de armazenamento baseados na nuvem, como o armazenamento Azure Blob através de um canal HTTPS seguro.

## <a name="prerequisites"></a>Pré-requisitos

- As versões suportadas do Windows são:
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

A instalação do tempo de integração auto-hospedado num controlador de domínio não é suportada.

- O tempo de integração auto-organizado requer um Sistema Operativo de 64 bits com .NET Framework 4.7.2 ou superior. Consulte [os requisitos do sistema-quadro .NET](/dotnet/framework/get-started/system-requirements) para obter mais informações.
- A configuração mínima recomendada para a máquina de tempo de execução de integração auto-hospedada é um processador de 2-GHz com 4 núcleos, 8 GB de RAM e 80 GB de espaço de disco rígido disponível. Para obter os detalhes dos requisitos do sistema, consulte [Download](https://www.microsoft.com/download/details.aspx?id=39717).
- Se a máquina hospedeira hibernar, o tempo de integração auto-hospedado não responde aos pedidos de dados. Configure um plano de alimentação apropriado no computador antes de instalar o tempo de funcionamento da integração auto-acolôda. Se a máquina estiver configurada para hibernar, o instalador de tempo de funcionação de integração auto-hospedado solicita com uma mensagem.
- Tem de ser um administrador na máquina para instalar e configurar com sucesso o tempo de funcionamento da integração auto-hospedada.
- As operações de cópia acontecem com uma frequência específica. O uso do processador e da RAM na máquina segue o mesmo padrão com tempos de pico e inatividade. O uso de recursos também depende muito da quantidade de dados que são movidos. Quando vários trabalhos de cópia estão em andamento, você vê o uso de recursos subir em tempos de pico.
- As tarefas podem falhar durante a extração de dados nos formatos Parquet, ORC ou Avro. Para mais informações sobre o Parquet, consulte [o formato Parquet na Azure Data Factory.](./format-parquet.md#using-self-hosted-integration-runtime) A criação de ficheiros funciona na máquina de integração auto-hospedada. Para funcionar como esperado, a criação de ficheiros requer os seguintes pré-requisitos:
  - [Visual C++ 2010 Redistributável](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pacote (x64)
  - Java Runtime (JRE) versão 8 de um fornecedor JRE como [Adopt OpenJDK](https://adoptopenjdk.net/). Certifique-se de que a `JAVA_HOME` variável ambiente está definida para a pasta JRE (e não apenas para a pasta JDK).

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Criação de um tempo de integração auto-hospedado

Para criar e configurar um tempo de integração auto-hospedado, utilize os seguintes procedimentos.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Criar um IR auto-hospedado via Azure PowerShell

1. Pode utilizar a Azure PowerShell para esta tarefa. Segue-se um exemplo:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Descarregue](https://www.microsoft.com/download/details.aspx?id=39717) e instale o tempo de integração auto-hospedado numa máquina local.

3. Recupere a chave de autenticação e registe o tempo de integração auto-hospedado com a chave. Aqui está um exemplo powerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Criar um IR auto-hospedado via Azure Data Factory UI

Utilize os seguintes passos para criar um IR auto-hospedado utilizando uI da Fábrica de Dados Azure.

1. Na página **Let's get start** of Azure Data Factory UI, selecione o [separador Gerir](./author-management-hub.md) a partir do painel mais à esquerda.

   ![O botão de gerir a página inicial](media/doc-common-process/get-started-page-manage-button.png)

1. Selecione **os tempos de integração** no painel esquerdo e, em seguida, selecione **+New**.

   ![Criar um integration runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na página de configuração do tempo de execução da **Integração,** selecione **Azure, Self-Hosted** e, em seguida, selecione **Continue**.

1. Na página seguinte, selecione **Self-Hosted** para criar um Self-Hosted IR e, em seguida, selecione **Continue**.
   ![Criar um IR auto-achatado](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. Insira um nome para o seu IR e selecione **Criar**.

1. Na página de configuração do tempo de execução da **integração,** selecione o link na **Opção 1** para abrir a configuração expressa no seu computador. Ou siga os passos na **Opção 2** para configurar manualmente. As seguintes instruções baseiam-se na configuração manual:

   ![Configuração do runtime de integração](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Copiar e colar a chave de autenticação. Selecione **Descarregar e instalar o tempo de execução da integração**.

    1. Transfira o integration runtime autoalojado num computador windows local. Execute o instalador.

    1. Na página **'Tempo de execução da integração do registo' (auto-hospedado),** cole a chave que guardou anteriormente e selecione **Registar**.

       ![Registar o integration runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na página de nó de novo número **de integração (auto-hospedada),** selecione **Finish**.

1. Depois de o tempo de integração auto-hospedado ser registado com sucesso, vê a seguinte janela:

    ![Registo com êxito](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Configurar um IR auto-hospedado num Azure VM através de um modelo de Gestor de Recursos Azure

Pode automatizar a configuração de IR auto-hospedada numa máquina virtual Azure utilizando o [modelo IV do anfitrião automático](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). O modelo fornece uma maneira fácil de ter um IR totalmente funcional auto-hospedado dentro de uma rede virtual Azure. O IR tem características de alta disponibilidade e escalabilidade, desde que desemba a contagem de nós para 2 ou mais.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Criar um IR auto-hospedado existente através do PowerShell local

Pode utilizar uma linha de comando para configurar ou gerir um IR auto-hospedado existente. Esta utilização pode ajudar especialmente a automatizar a instalação e o registo de nós de INFRAVERMELHOs auto-hospedados.

Dmgcmd.exe está incluído no instalador auto-hospedado. Está tipicamente localizado na pasta C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ . Esta aplicação suporta vários parâmetros e pode ser invocada através de uma linha de comando utilizando scripts de lote para automatização.

Utilize a aplicação da seguinte forma:

```powershell
dmgcmd ACTION args...
```

Aqui estão os detalhes das ações e argumentos da aplicação: 

|AÇÃO|args|Description|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|Registe um nó de tempo de execução de integração auto-hospedado com a chave de autenticação especificada e o nome do nó.|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|Permitir o acesso remoto no nó atual para configurar um cluster de alta disponibilidade. Ou ativar a definição de credenciais diretamente contra o IR auto-hospedado sem passar pela Azure Data Factory. Faz este último utilizando o cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** a partir de uma máquina remota na mesma rede.|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|Ativar o acesso remoto ao nó de corrente quando o nó estiver num recipiente.|
|`-dra`,<br/>`-DisableRemoteAccess`||Desative o acesso remoto ao nó de corrente. É necessário um acesso remoto para a configuração multinode. O **cmdlet New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell ainda funciona mesmo quando o acesso remoto é desativado. Este comportamento é verdadeiro desde que o cmdlet seja executado na mesma máquina que o nó IV auto-hospedado.|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|Substitua ou atualize a chave de autenticação anterior. Cuidado com esta ação. O seu nó IV auto-hospedado anteriormente pode ficar offline se a chave for de um novo tempo de integração.|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|Gere um ficheiro de reserva para o nó atual. O ficheiro de cópia de segurança inclui a chave do nó e as credenciais de loja de dados.|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|Restaurar o nó de um ficheiro de reserva.|
|`-r`,<br/>`-Restart`||Reinicie o serviço de anfitrião de execução de integração auto-hospedado.|
|`-s`,<br/>`-Start`||Inicie o serviço de anfitrião de execução de integração auto-hospedado.|
|`-t`,<br/>`-Stop`||Pare o serviço de anfitrião de execução de integração auto-hospedado.|
|`-sus`,<br/>`-StartUpgradeService`||Inicie o serviço de atualização de tempo de integração auto-hospedado.|
|`-tus`,<br/>`-StopUpgradeService`||Pare o serviço de atualização de tempo de integração auto-hospedado.|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||Ligue a atualização automática do tempo de execução da integração auto-hospedada.|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||Desligue a atualização automática do tempo de execução da integração auto-hospedada.|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|Defina o DIAHostService para funcionar como uma nova conta. Utilize a palavra-passe vazia "" para contas do sistema e contas virtuais.|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instale e registe um IR auto-hospedado do Microsoft Download Center

1. Aceda à página de [descarregamento de tempo de execução](https://www.microsoft.com/download/details.aspx?id=39717)da integração da Microsoft.
2. Selecione **Download**, selecione a versão de 64 bits e selecione **Next**. A versão de 32 bits não é suportada.
3. Executar o ficheiro Identidade Gerida diretamente, ou guarde-o para o seu disco rígido e execute-o.
4. Na janela **Welcome,** selecione um idioma e selecione **Seguinte**.
5. Aceite os Termos de Licença de Software da Microsoft e selecione **Seguinte**.
6. Selecione **a pasta** para instalar o tempo de execução de integração auto-hospedado e selecione **Next**.
7. Na página **Pronto para instalar,** selecione **Instalar**.
8. **Selecione Acabamento** para concluir a instalação.
9. Obtenha a chave de autenticação utilizando o PowerShell. Aqui está um exemplo powerShell para recuperar a chave de autenticação:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. Na janela **de runtime de integração do registo (self-hosted)** do Gestor de Configuração de Tempo de Execução de Integração da Microsoft em funcionamento na sua máquina, tome os seguintes passos:

    1. Cole a chave de autenticação na área de texto.

    2. Opcionalmente, selecione **Mostrar a chave de autenticação** para ver o texto chave.

    3. Selecione **Registar**.

## <a name="service-account-for-self-hosted-integration-runtime"></a>Conta de serviço para o tempo de integração auto-hospedado

O registo predefinido na conta de serviço do tempo de execução de integração auto-hospedado é **NT SERVICE\DIAHostService**. Pode vê-lo em **Serviços de > Serviço de Integração -> Propriedades -> Iniciar sessão**.

![Conta de serviço para o tempo de integração auto-hospedado](media/create-self-hosted-integration-runtime/shir-service-account.png)

Certifique-se de que a conta tem a permissão de Iniciar sessão como um serviço. Caso contrário, o tempo de integração auto-hospedado não pode começar com sucesso. Pode consultar a permissão em **Definições de Segurança > -> Políticas Locais -> Atribuição de Direitos de Utilizador -> Iniciar sessão como serviço**

![Screenshot da Política de Segurança Local - Atribuição de Direitos de Utilizador](media/create-self-hosted-integration-runtime/shir-service-account-permission.png)

![Screenshot de Iniciar sessão como uma atribuição de direitos de utilizador de serviço](media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png)

## <a name="notification-area-icons-and-notifications"></a>Ícones e notificações da área de notificação

Se deslocar o cursor sobre o ícone ou mensagem na área de notificação, pode ver detalhes sobre o estado do tempo de integração auto-hospedado.

![Notificações na área de notificação](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="high-availability-and-scalability"></a>Alta disponibilidade e escalabilidade

Pode associar um tempo de integração auto-hospedado a várias máquinas no local ou máquinas virtuais em Azure. Estas máquinas são chamadas nós. Você pode ter até quatro nós associados a um tempo de integração auto-hospedado. Os benefícios de ter múltiplos nóns em máquinas no local que têm um gateway instalado para um gateway lógico são:

- Maior disponibilidade do tempo de integração auto-hospedado para que não seja mais o único ponto de falha na sua solução de big data ou integração de dados em nuvem com a Data Factory. Esta disponibilidade ajuda a garantir a continuidade quando utiliza até quatro nós.
- Melhor desempenho e produção durante o movimento de dados entre as lojas de dados no local e as lojas de dados em nuvem. Obtenha mais informações sobre [comparações de desempenho.](copy-activity-performance.md)

Pode associar vários nós instalando o software de execução de integração auto-hospedado do [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Em seguida, registe-o utilizando uma das chaves de autenticação obtidas a partir do cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey,** conforme descrito no [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Não é necessário criar um novo tempo de integração auto-hospedado para associar cada nó. Pode instalar o tempo de integração auto-hospedado noutra máquina e registá-lo utilizando a mesma chave de autenticação.

> [!NOTE]
> Antes de adicionar outro nó para alta disponibilidade e escalabilidade, certifique-se de que o acesso remoto à opção **intranet** está ativado no primeiro nó. Para tal, selecione **Microsoft Integration Runtime Configuration Manager**  >  **Configurações** Acesso remoto à  >  **intranet**.

### <a name="scale-considerations"></a>Considerações de escala

#### <a name="scale-out"></a>Aumentar horizontalmente

Quando a utilização do processador é elevada e a memória disponível é baixa no IR auto-hospedado, adicione um novo nó para ajudar a escalar a carga através das máquinas. Se as atividades falharem porque estão fora de tempo ou o nó IR auto-hospedado estiver offline, ajuda se adicionar um nó ao gateway.

#### <a name="scale-up"></a>Aumentar verticalmente

Quando o processador e a RAM disponível não são bem utilizados, mas a execução de empregos simultâneos atinge os limites de um nó, aumentando o número de empregos simultâneos que um nó pode executar. Você também pode querer aumentar quando as atividades estão fora porque o IR auto-hospedado está sobrecarregado. Como mostrado na imagem seguinte, pode aumentar a capacidade máxima para um nó:  

![Aumentar o número de empregos simultâneos que podem funcionar num nó](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos de certificado TLS/SSL

Aqui estão os requisitos para o certificado TLS/SSL que utiliza para garantir a comunicação entre nós de tempo de integração:

- O certificado deve ser um certificado X509 v3 de confiança pública. Recomendamos que utilize certificados emitidos por uma autoridade de certificação de parceiros públicos (CA).
- Cada nó de tempo de integração deve confiar neste certificado.
- Não recomendamos certificados de Nome Alternativo Sujeito (SAN) porque apenas o último artigo SAN é usado. Todos os outros itens da SAN são ignorados. Por exemplo, se tiver um certificado SAN cujos SANs são **node1.domain.contoso.com** e **node2.domain.contoso.com,** pode utilizar este certificado apenas numa máquina cujo nome de domínio totalmente qualificado (FQDN) é **node2.domain.contoso.com**.
- O certificado pode utilizar qualquer tamanho de chave suportado pelo Windows Server 2012 R2 para certificados TLS/SSL.
- Os certificados que usam chaves de CNG não são suportados.  

> [!NOTE]
> Este certificado é utilizado:
>
> - Para encriptar portas num nó DE IR auto-hospedado.
> - Para comunicação nó-a-nó para a sincronização do estado, que inclui a sincronização de credenciais de serviços ligados através de nós.
> - Quando um cmdlet PowerShell é utilizado para configurações credenciais de serviço ligado a partir de uma rede local.
>
> Sugerimos que utilize este certificado se o seu ambiente de rede privada não for seguro ou se pretender assegurar a comunicação entre nós dentro da sua rede privada.
>
> O movimento de dados em trânsito de um IR auto-alojado para outras lojas de dados acontece sempre dentro de um canal encriptado, independentemente de este certificado estar ou não definido.

## <a name="proxy-server-considerations"></a>Considerações do servidor proxy

Se o seu ambiente de rede corporativa utilizar um servidor proxy para aceder à internet, configure o tempo de execução de integração auto-hospedado para utilizar as definições de procuração apropriadas. Pode definir o representante durante a fase inicial de registo.

![Especificar o representante](media/create-self-hosted-integration-runtime/specify-proxy.png)

Quando configurado, o tempo de integração auto-hospedado utiliza o servidor proxy para ligar à origem e destino do serviço de nuvem (que utilizam o protocolo HTTP ou HTTPS). É por isso que seleciona **Alterar o link** durante a configuração inicial.

![Definir o proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Existem três opções de configuração:

- **Não utilize proxy**: O tempo de integração auto-hospedado não utiliza explicitamente qualquer procuração para se ligar a serviços na nuvem.
- **Utilização do sistema proxy**: O tempo de execução de integração auto-hospedado utiliza a definição de procuração configurada em diahost.exe.config e diawp.exe.config. Se estes ficheiros especificarem nenhuma configuração de procuração, o tempo de execução de integração auto-hospedado liga-se diretamente ao serviço de nuvem sem passar por um representante.
- **Utilize o proxy personalizado**: Configure a definição de procuração HTTP para utilizar para o tempo de execução de integração auto-hospedado, em vez de utilizar configurações em diahost.exe.config e diawp.exe.config. **São** necessários valores de endereço e **porta.** **Os** valores do Nome de Utilizador e da **Palavra-Passe** são opcionais, dependendo da definição de autenticação do seu representante. Todas as definições são encriptadas com O DPAPI do Windows no tempo de funcionação de integração auto-hospedado e armazenadas localmente na máquina.

O serviço de anfitrião de execução de integração reinicia automaticamente depois de guardar as definições de procuração atualizadas.

Depois de registar o tempo de execução de integração auto-hospedado, se pretender visualizar ou atualizar as definições de procuração, utilize o Gestor de Configuração de Tempo de Execução da Integração da Microsoft.

1. Abra **o Gestor de Configuração de Tempo de Execução da Integração da Microsoft**.
1. Selecione o separador **Settings** (Definições).
1. Em **HTTP Proxy**, selecione o link **'Alterar'** para abrir a caixa de diálogo **'set HTTP Proxy'.**
1. Selecione **Seguinte**. Em seguida, vê um aviso que pede a sua permissão para salvar a definição de procuração e reiniciar o serviço de anfitrião de tempo de integração.

Pode utilizar a ferramenta do gestor de configuração para visualizar e atualizar o proxy HTTP.

![Ver e atualizar o proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Se configurar um servidor proxy com autenticação NTLM, o serviço de anfitrião de execução de integração funciona sob a conta de domínio. Se mais tarde alterar a palavra-passe para a conta de domínio, lembre-se de atualizar as definições de configuração do serviço e reiniciar o serviço. Devido a este requisito, sugerimos que aceda ao servidor proxy utilizando uma conta de domínio dedicada que não requer que atualize a palavra-passe com frequência.

### <a name="configure-proxy-server-settings"></a>Configurar configurações de servidor de procuração

Se selecionar a opção **proxy do sistema Utilização** para o proxy HTTP, o tempo de execução de integração auto-hospedado utiliza as definições de procuração em diahost.exe.config e diawp.exe.config. Quando estes ficheiros especificam nenhum proxy, o tempo de integração auto-hospedado conecta-se diretamente ao serviço de nuvem sem passar por um representante. O procedimento a seguir fornece instruções para atualizar o ficheiro diahost.exe.config:

1. No File Explorer, faça uma cópia segura de C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config como uma cópia de segurança do ficheiro original.
1. Abrir o Bloco de Notas funcionando como administrador.
1. No Bloco de Notas, abra o ficheiro de texto C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config.
1. Encontre a etiqueta **de system.net** padrão como mostrado no seguinte código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Em seguida, pode adicionar detalhes do servidor proxy como mostrado no seguinte exemplo:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    A etiqueta proxy permite que propriedades adicionais especifiquem as definições necessárias como `scriptLocation` . Consulte [ \<proxy\> o elemento (Definições de rede)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) para a sintaxe.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. Guarde o ficheiro de configuração na sua localização original. Em seguida, reinicie o serviço de anfitrião de execução de integração auto-hospedado, que capta as alterações.

   Para reiniciar o serviço, utilize os serviços applet do Painel de Controlo. Ou do Gestor de Configuração de Tempo de Execução de Integração, selecione o botão **'Serviço de paragem'** e, em seguida, selecione **'Iniciar'.**

   Se o serviço não arrancar, é provável que tenha adicionado sintaxe de marca XML incorreta no ficheiro de configuração da aplicação que editou.

> [!IMPORTANT]
> Não se esqueça de atualizar diahost.exe.config e diawp.exe.config.

Também precisa de se certificar de que o Microsoft Azure está na lista de autorizações da sua empresa. Você pode baixar a lista de endereços IP válidos do [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=41653)

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Possíveis sintomas para problemas relacionados com a firewall e servidor de procuração

Se vir mensagens de erro como as seguintes, a razão provável é a configuração imprópria da firewall ou servidor de procuração. Esta configuração impede que o tempo de integração auto-hospedado se conecte à Data Factory para se autenticar. Para garantir que a sua firewall e o servidor proxy estão corretamente configurados, consulte a secção anterior.

- Quando tenta registar o tempo de integração auto-hospedado, recebe a seguinte mensagem de erro: "Falhou no registo deste nó de tempo de execução de integração! Confirme que a chave autenticação é válida e que o serviço de assistência de integração está a funcionar nesta máquina."
- Quando abre o Gestor de Configuração de Tempo de Execução de Integração, vê um estado de **Desconectado** ou **De Ligação**. Quando visualiza os registos de eventos do Windows, em **Aplicação** e Serviços do Espectador de  >  **Eventos,** o  >  **Microsoft Integration Runtime**, vê mensagens de erro como esta:

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>Permitir o acesso remoto a partir de uma intranet

Se utilizar o PowerShell para encriptar credenciais de uma máquina em rede diferente do local onde instalou o tempo de funciona de integração auto-hospedado, pode ativar o acesso remoto a partir da opção **Intranet.** Se executar o PowerShell para encriptar credenciais na máquina onde instalou o tempo de funcionação de integração auto-hospedado, não pode ativar o **Acesso Remoto a partir da Intranet**.

Ativar **o Acesso Remoto a partir da Intranet** antes de adicionar outro nó para alta disponibilidade e escalabilidade.  

Quando executar a versão de configuração de tempo de execução de integração auto-hospedada 3.3 ou posterior, por padrão, o instalador de tempo de integração auto-hospedado desativa o **Acesso Remoto da Intranet** na máquina de execução de integração auto-hospedada.

Quando utilizar uma firewall de um parceiro ou de outros, pode abrir manualmente a porta 8060 ou a porta configurada pelo utilizador. Se tiver um problema de firewall durante a configuração do tempo de funcionamento da integração auto-hospedada, utilize o seguinte comando para instalar o tempo de integração auto-hospedado sem configurar a firewall:

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Se optar por não abrir a porta 8060 na máquina de tempo de funcionamento de integração auto-hospedada, utilize mecanismos que não a aplicação 'Definição de Credenciais' para configurar credenciais de armazenamento de dados. Por exemplo, pode utilizar o **cmdlet New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell.

## <a name="ports-and-firewalls"></a>Portas e firewalls

Há duas firewalls a considerar:

- A *firewall corporativa* que funciona no router central da organização
- A *firewall do Windows* que é configurada como um daemon na máquina local onde o tempo de integração auto-hospedado é instalado

![As firewalls](media/create-self-hosted-integration-runtime/firewall.png)

Ao nível da firewall corporativa, é necessário configurar os seguintes domínios e portas de saída:

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

Ao nível da firewall do Windows ou ao nível da máquina, estas portas de saída estão normalmente ativadas. Se não forem, pode configurar os domínios e portas numa máquina de execução de integração auto-hospedada.

> [!NOTE]
> Como atualmente o Azure Relay não suporta a etiqueta de serviço, tem de utilizar a etiqueta de serviço **AzureCloud** ou **a Internet** nas regras NSG para a comunicação ao Azure Relay.
> Para a comunicação com a Azure Data Factory, pode utilizar a etiqueta de serviço **DataFactoryManagement** na configuração da regra NSG.

Com base na sua fonte e pias, poderá ter de permitir domínios adicionais e portas de saída na sua firewall corporativa ou firewall do Windows.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

Para algumas bases de dados em nuvem, como a Base de Dados Azure SQL e o Lago de Dados Azure, poderá ser necessário permitir endereços IP de máquinas de execução de integração auto-hospedadas na sua configuração de firewall.

### <a name="get-url-of-azure-relay"></a>Obtenha URL de Azure Relay

Um domínio e uma porta necessários que precisam de ser colocados na lista de permitir a sua firewall é para a comunicação ao Azure Relay. O tempo de execução de integração auto-hospedado utiliza-o para a autoria interativa, tais como a ligação de teste, a lista de pastas de navegação e a lista de tabelas, obter esquemas e dados de pré-visualização. Se você não quer permitir **.servicebus.windows.net** e gostaria de ter URLs mais específicos, então você pode ver todos os FQDNs que são necessários pelo seu tempo de integração auto-hospedado a partir do portal ADF. Siga estes passos:

1. Vá ao portal ADF e selecione o seu tempo de integração auto-hospedado.
2. Na página Editar, selecione **Nós**.
3. Selecione **Ver URLs** de serviço para obter todos os FQDNs.

   ![URLs de relé Azure](media/create-self-hosted-integration-runtime/Azure-relay-url.png)

4. Pode adicionar estes FQDNs na lista de autorizações de regras de firewall.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copiar dados de uma fonte para um lavatório

Certifique-se de que permite corretamente as regras de firewall na firewall corporativa, a firewall do Windows da máquina de tempo de execução de integração auto-hospedada e a própria loja de dados. Permitir estas regras permite que o tempo de execução de integração auto-organizado se conecte com sucesso tanto à fonte como à pia. Ativar as regras para cada loja de dados que esteja envolvida na operação de cópia.

Por exemplo, para copiar de uma loja de dados no local para um lavatório SQL Database ou um lavatório Azure Synapse Analytics, tome os seguintes passos:

1. Permitir a comunicação TCP de saída na porta 1433 para a firewall do Windows e para a firewall corporativa.
2. Configure as definições de firewall da Base de Dados SQL para adicionar o endereço IP da máquina de execução de integração auto-hospedada à lista de endereços IP permitidos.

> [!NOTE]
> Se a sua firewall não permitir a porta de saída 1433, o tempo de execução de integração auto-hospedado não pode aceder diretamente à base de dados SQL. Neste caso, pode utilizar uma [cópia encenada](copy-activity-performance.md) para a SQL Database e para a Azure Synapse Analytics. Neste cenário, é necessário apenas HTTPS (porta 443) para o movimento de dados.

## <a name="installation-best-practices"></a>Melhores práticas de instalação

Pode instalar o tempo de integração auto-hospedado descarregando um pacote de configuração de identidade gerida do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Consulte o artigo [Mova os dados entre as instalações e a nuvem](tutorial-hybrid-copy-powershell.md) para obter instruções passo a passo.

- Configure um plano de energia na máquina hospedeira para o tempo de integração auto-hospedado para que a máquina não se hiberna. Se a máquina hospedeira hibernar, o tempo de integração auto-hospedado fica offline.
- Ressari regularmente as credenciais associadas ao tempo de integração auto-hospedado.
- Para automatizar as operações de configuração de INFRAVERMELHOs auto-hospedadas, consulte a [Configuração de um IR auto-hospedado existente via PowerShell](#setting-up-a-self-hosted-integration-runtime).

## <a name="next-steps"></a>Passos seguintes

Para obter instruções passo a passo, consulte [Tutorial: Copie os dados no local para cloud](tutorial-hybrid-copy-powershell.md).
