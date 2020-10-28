---
title: Resolução de problemas de integração auto-acolagem na Azure Data Factory
description: Saiba como resolver problemas de integração auto-hospedados na Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/26/2020
ms.author: lle
ms.openlocfilehash: 3598db409e5493737753a8a1b03de168af5c664b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637195"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Resolução de problemas de integração auto-acolagem

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para o tempo de integração auto-hospedado na Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Recolha registos de IR auto-hospedados da Azure Data Factory

Para atividades falhadas em execução em IR /ID Partilhado, a Azure Data Factory suporta a visualização e o upload de registos de erros. Pode seguir os passos abaixo para obter a identificação do relatório de erro e, em seguida, inserir o ID do relatório para localizar questões conhecidas relacionadas.

1. Ir para a página **de execuções de atividades.**

1. Sob a coluna **ERROR,** clique abaixo do botão.

    ![A atividade executa a página](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. Verá registos relacionados para a execução da atividade falhada. Clique no botão **Enviar registos** para obter mais assistência.

    ![Enviar registos](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. Pode escolher registos que pretende enviar. Para *o IR auto-hospedado,* pode carregar registos relacionados com a atividade falhada ou todos os registos no nó IV auto-hospedado. Para *o IR Partilhado,* só é possível carregar registos relacionados com a atividade falhada.

    ![Escolha registos](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Quando os registos estiverem a ser enviados, mantenha um registo do ID do relatório se precisar de mais assistência para resolver o problema.

    ![Carregar registos](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Os pedidos de visualização e upload de registos serão executados em todas as instâncias ir auto-hospedadas online. Por favor, certifique-se de que todas as instâncias de IR auto-hospedadas estão on-line no caso de quaisquer registos em falta. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Erro ou falha geral do IR Autoalojado

### <a name="tlsssl-certificate-issue"></a>Problema com o certificado TLS/SSL

#### <a name="symptoms"></a>Sintomas

Ao tentar ativar o certificado TLS/SSL (avançado) no **Configuration Manager do IR Autoalojado** -> **Acesso remoto a partir da intranet** , depois de selecionar o certificado TLS/SSL, é apresentado o erro abaixo:

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

No caso acima, o utilizador está a utilizar o certificado com “microsoft.com” como último item.

#### <a name="cause"></a>Causa

Este é um problema conhecido no WCF: a validação TLS/SSL do WCF verifica apenas o último DNSName na SAN. 

#### <a name="resolution"></a>Resolução

O certificado de caráter universal é suportado no IR Autoalojado do Azure Data Factory v2. Este problema normalmente ocorre porque o certificado SSL não está correto. O último DNSName na SAN deve ser válido. Siga os passos abaixo para o verificar. 
1.  Consola de Gestão Aberta, verifique duplamente o Nome Alternativo *do Assunto* e *do Assunto* a partir dos Detalhes do Certificado. Acima, por exemplo, o último item em *Nome Alternativo Sujeito* , que é "DNS Name= microsoft.com.com", não é legítimo.
2.  Contacte a empresa de emissão de certificados para remover o nome DNS errado.

### <a name="concurrent-jobs-limit-issue"></a>Problema de limite de trabalhos simultâneos

#### <a name="symptoms"></a>Sintomas

Ao tentar aumentar o limite dos trabalhos simultâneos da IU do Azure Data Factory, ocorre um bloqueio como *a atualizar* indefinidamente.
O valor máximo de trabalhos simultâneos foi definido como 24. Deve aumentar a contagem para que os trabalhos possam ser executados mais rapidamente. O valor mínimo que pode introduzir é 3 e o valor máximo é 32. Aumentou o valor de 24 para 32 e premiu no botão *de atualização,* no UI ficou preso na *atualização* como pode ver abaixo. Após a atualização, o cliente ainda viu o valor como 24 e nunca foi atualizado para 32.

![Estado de atualização](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

Há uma limitação para a definição, uma vez que o valor depende do computador logicCore e da Memória. Assim, pode apenas ajustá-lo para um valor inferior, como 24, e ver o resultado.

> [!TIP] 
> - Para mais detalhes sobre o que é a contagem do núcleo lógico, e como encontrar a contagem de núcleos lógicos da nossa máquina, consulte [este artigo.](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)
> - Para mais detalhes sobre como calcular o math.log, consulte [este artigo](https://www.rapidtables.com/calc/math/Log_Calculator.html).


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>Problema do Certificado SSL de HA do IR Autoalojado

#### <a name="symptoms"></a>Sintomas

O nó de trabalho do IR Autoalojado apresentou o erro abaixo:

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>Causa

Quando processamos casos relacionados com o handshake do SSL/TLS, podemos encontrar alguns problemas relacionados com a verificação da cadeia de certificados. 

#### <a name="resolution"></a>Resolução

- Aqui está uma forma rápida e intuitiva de resolver problemas X.509 falha na cadeia de certificação X.509.
 
    1. Exporte o certificado, que precisa de ser verificado. Aceda a Gerir certificado do computador, localize o certificado que quer verificar e clique com o botão direito do rato em **Todas as tarefas** -> **Exportar** .
    
        ![Tarefas de exportação](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copie o certificado exportado para a máquina cliente. 
    3. No lado do cliente, execute o comando abaixo no CMD. Certifique-se de que substituiu abaixo *\<certificate path>* e que os espaços *\<output txt file path>* reservados por caminhos relacionados.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Por exemplo:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Verifique se há algum erro no ficheiro txt de saída. Pode encontrar o resumo dos erros no final do ficheiro txt.

        Por exemplo: 

        ![Resumo do erro](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se não vir qualquer erro no final do ficheiro de registo, como mostrado abaixo, pode considerar a cadeia de certificados acumulada com sucesso na máquina do cliente.
        
        ![Nenhum erro no ficheiro de registo](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Se houver AIA, CDP e OCSP configurados no ficheiro de certificado. Podemos verificar de uma forma mais intuitiva.
 
    1. Pode obter esta informação verificando os detalhes de um certificado.
    
        ![Detalhe do certificado](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. Execute o comando abaixo. Certifique-se de que substituiu *\<certificate path>* o espaço reservado por um percurso relacionado com o certificado.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. Em seguida, a **ferramenta de Recuperação de URL** será aberta. Pode verificar os certificados do AIA, CDP e OCSP ao clicar no botão **Recuperar** .

        ![Botão de recuperação](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        A cadeia de certificados poderá ser criada com êxito se o certificado do AIA estiver “Verificado” e o certificado do CDP ou OCSP estiver “Verificado”.

        Se encontrar uma falha ao recuperar o AIA, CDP, trabalhe com a equipa de rede para deixar o computador cliente pronto para se ligar ao URL de destino. Será suficiente se o caminho http ou o caminho LDAP puder ser verificado.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>O IR Autoalojado não conseguiu carregar o ficheiro ou a assemblagem

#### <a name="symptoms"></a>Sintomas

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
Por exemplo: 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>Causa

Se tomar o monitor de processos, pode ver o seguinte resultado:

[![Monitor de processo](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> Pode definir o filtro como mostrado na imagem abaixo.
> Diz-nos que o dll **System.ValueTuple** não está localizado na pasta relacionada com o GAC, nem em *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* , ou em *C:\Program Files\Microsoft Integration Runtime\4.0\Pasta partilhada.*
> Basicamente, carregará o DLL da pasta *GAC* primeiro e, em seguida, da pasta *Partilhado* e, por fim, da pasta *Gateway* . Portanto, pode colocar o DLL em qualquer caminho que possa ser útil.

![Configurar filtros](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Resolução

Pode descobrir que o **System.ValueTuple.dll** está localizado em *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan.* Copie a **System.ValueTuple.dll** para *C:\Ficheiros de programa\Microsoft Integration Runtime\4.0\Gateway* para resolver o problema.

Pode utilizar o mesmo método para resolver outros problemas de ficheiros ou assemblagens em falta.

#### <a name="more-information"></a>Mais Informações

A razão pela qual vê o System.ValueTuple.dll em *%windir%\Microsoft.NET\montagem* e *%windir%\montagem* é que se trata de um comportamento .NET. 

A partir do erro abaixo, pode ver claramente o *sistema de montagem.ValueTuple* não está lá. Assim, tal questão acontece quando a aplicação tenta verificar a montagem *System.ValueTuple.dll* .
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
Para mais informações sobre o GAC, consulte [este artigo.](/dotnet/framework/app-domains/gac)


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>Como auditar a chave do IR Autoalojado em falta

#### <a name="symptoms"></a>Sintomas

O runtime de integração autoalojado passa de repente para offline sem chave e a mensagem de erro abaixo é apresentada no Registo de Eventos: `Authentication Key is not assigned yet`

![Chave de autenticação em falta](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- O nó do IR Autoalojado ou o IR Autoalojado lógico no portal é eliminado.
- É feita uma desinstalação limpa.

#### <a name="resolution"></a>Resolução

Se nenhuma das causas acima referidas se aplicar, pode ir à pasta: *%programdata%\Microsoft\Transferência de dados\DataManagementGateway,* e verificar se o ficheiro denominado **Configurações** é eliminado. Se tiver sido eliminado, siga as instruções [aqui](https://www.netwrix.com/how_to_detect_who_deleted_file.html) para auditar quem elimina o ficheiro.

![Verifique o ficheiro configurações](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>Não pode utilizar o IR Autoalojado para ligar dois arquivos de dados no local

#### <a name="symptoms"></a>Sintomas

Depois de criar os IRs Autoalojados para os arquivos de dados de origem e de destino, deve ligar os dois IRs em conjunto para concluir uma cópia. Se as lojas de dados estiverem configuradas em VNETs diferentes, ou não conseguirem compreender o mecanismo de gateway, irá atingir erros como: *o condutor da fonte não pode ser encontrado no destino IR;* *a fonte não pode ser acedida pelo destino IR* .
 
#### <a name="cause"></a>Causa

O IR Autoalojado foi criado como um nó central de uma atividade de cópia, não um agente cliente que precisa de ser instalado para cada arquivo de dados.
 
No caso acima, o serviço ligado a cada arquivo de dados deve ser criado com o mesmo IR e o IR deve conseguir aceder a ambos os arquivos de dados através da rede. Independentemente de o IR ser instalado com o arquivo de dados de origem, o arquivo de dados de destino ou num terceiro computador, se dois serviços ligados forem criados com IRs diferentes, mas forem utilizados na mesma atividade de cópia, o IR de destino será usado e os controladores para os dois arquivos de dados precisarão de ser instalados no computador IR de destino.

#### <a name="resolution"></a>Resolução

Instale os controladores para a origem e o destino no IR de destino e verifique se consegue aceder ao arquivo de dados de origem.
 
Se o tráfego não puder passar pela rede entre dois arquivos de dados (por exemplo, se estiverem configurados em duas VNETs), não poderá concluir a cópia numa atividade mesmo com o IR instalado. Nesse caso, pode criar duas atividades de cópia com dois IRs, cada um num VENT: 1 IR para copiar a partir do arquivo de dados 1 para o Armazenamento de Blobs do Azure, outro para copiar a partir do Armazenamento de Blobs do Azure para o arquivo de dados 2. Deste modo, poderia simular a condição de utilizar o IR para criar uma ponte que liga dois arquivos de dados desligados.


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>O problema de sincronização de credenciais causa a perda de credenciais da HA

#### <a name="symptoms"></a>Sintomas

A credencial de origem de dados “XXXXXXXXXX” foi eliminada do nó do Integration Runtime atual com o payload “ao eliminar o serviço de ligação no portal do Azure ou a tarefa tem o payload errado, crie o novo serviço de ligação com a credencial novamente”.

#### <a name="cause"></a>Causa

O IR Autoalojado é criado no modo HA com dois nós, mas não estão no estado de sincronização de credenciais, o que significa que as credenciais armazenadas no nó Dispatcher não são sincronizadas com outros nós de trabalho. Se alguma ativação pós-falha ocorrer do nó Dispatcher para o nó de trabalho, mas as credenciais existirem apenas no nó Dispatcher anterior, a tarefa falhará ao tentar aceder às credenciais e obterá o erro acima.

#### <a name="resolution"></a>Resolução

A única forma de evitar este problema é verificar se dois nós estão no estado de sincronização de credenciais. Caso contrário, tem de reintroduzir as credenciais para o novo Dispatcher.


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>Não é possível escolher o certificado devido à chave privada em falta

#### <a name="symptoms"></a>Sintomas

1.  Importe um ficheiro PFX para o arquivo de certificados.
2.  Ao selecionar o certificado através da IU do Configuration Manager do IR, obtém o erro abaixo:

    ![Chave privada desaparecida](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- A conta de utilizador tem privilégios baixos e não pode aceder à chave privada.
- O certificado foi gerado como assinatura, mas não como troca de chaves.

#### <a name="resolution"></a>Resolução

1.  Utilize uma conta com privilégios que possa aceder à chave privada para operar a IU.
2.  Execute o comando abaixo para importar o certificado:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>Configuração de IR auto-hospedada

### <a name="the-integration-runtime-registration-error"></a>O erro de registo do tempo de execução de integração 

#### <a name="symptoms"></a>Sintomas

Às vezes queremos executar o IR auto-hospedado numa conta diferente pelas razões abaixo:
- A política da empresa não permite a conta de serviço.
- É necessária alguma autenticação.

Depois de alterar a conta de serviço no Painel de Serviço, poderá descobrir que o tempo de funcionamento da integração deixa de funcionar.

![Erro de registo de IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Há muitos recursos que só são concedidos à conta de serviço. Ao mudar a conta de serviço para outra conta, a permissão de todos os recursos dependentes permanece a mesma.

#### <a name="resolution"></a>Resolução

Aceda ao registo de eventos de integração para verificar o erro.

![Diário de eventos do IR](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

Se o erro aparecer como acima *Não AutorizadoAccessExcepção,* siga as instruções abaixo:


1. Consulte a conta de serviço *diahostService* no painel de serviço do Windows.

    ![Conta de serviço de início de síl,](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Verifique se a conta de serviço de início de súmã tem a permissão R/W sobre a pasta: *%programdata%\Microsoft\DataTransfer\DataManagementGateway* .

    - Por predefinição, se a conta de início de sposição do serviço não tiver sido alterada, deverá ter a permissão de R/W.

        ![Permissão de serviço](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - Se alterou a conta de início de saúde do serviço, siga abaixo os passos para atenuar o problema:
        1. Limpe desinstalar o atual IR auto-hospedado.
        1. Instale as bits de IR auto-hospedadas.
        1. Siga abaixo as instruções para alterar a conta de serviço: 
            1. Vá para a pasta de instalação auto-achatada do IR, mude para a pasta: *Microsoft Integration Runtime\4.0\Shared* .
            1. Inicie uma linha de comando usando privilégios elevados. Substitua *\<user>* e pelo seu próprio nome de utilizador e senha *\<password>* e, em seguida, corra abaixo do comando:
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. Se pretender alterar para a conta Do Sistema Local, certifique-se de utilizar um formato correto para esta conta. Abaixo está um exemplo do formato correto:

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                **Não** utilize o formato como mostrado abaixo:

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. Para alternativa, uma vez que o Sistema Local tem um privilégio superior ao administrador, também pode alterá-lo diretamente em "Serviços".
            1. Pode utilizar o utilizador local/domínio para a conta de início de sísmia do serviço DE INFRAVERMELHO.            
        1. Registe o tempo de execução da integração.

Se o erro aparecer como: *O Serviço de Serviço 'Serviço de Verificação de Integração' (DIAHostService) não foi iniciado. Verifique se tem privilégios suficientes para iniciar os serviços do sistema,* siga as instruções abaixo:

1. Consulte a conta de serviço *diahostService* no painel de serviço do Windows.
   
    ![Conta de serviço de início de síl,](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. Verifique se a conta de serviço de início de sessão tem o Log como uma permissão **de serviço** para iniciar o Serviço do Windows:

    ![Logon como serviço](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mais Informações

Se nenhum dos dois padrões acima de dois padrões de resolução se aplicar no seu caso, tente recolher abaixo os registos do Evento do Windows: 
- Registos de aplicações e serviços -> tempo de funcionamento da integração
- Aplicação de registos do Windows ->

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>Não é possível encontrar o botão registar-se para registar um IR auto-hospedado    

#### <a name="symptoms"></a>Sintomas

O botão **Registar** não foi encontrado na UI do Gestor de Configuração ao registar um IR auto-hospedado.

![Sem botão de registo](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

Desde o lançamento do Tempo de Execução de *Integração 3.0,* o botão **registar-se** num nó de tempo de execução de integração existente foi removido para permitir um ambiente mais limpo e seguro. Se um nó tiver sido registado em algum Integration Runtime (online ou não), para o registar novamente noutro Integration Runtime, deverá desinstalar o nó anterior e, em seguida, instalar e registar o nó.

#### <a name="resolution"></a>Resolução

1. Vá ao painel de controlo para desinstalar o tempo de execução de integração existente.

    > [!IMPORTANT] 
    > No processo abaixo, selecione Sim. Não guarde os dados durante o processo de desinstalar.

    ![Eliminar dados](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se não tiver o MSI instalador de tempo de integração, vá [ao centro de descarregamento](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) para descarregar o mais recente Tempo de Execução de Integração.
1. Instale o MSI e registe o tempo de execução da integração.


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>Não é possível registar o IR Autoalojado devido a localhost    

#### <a name="symptoms"></a>Sintomas

Não é possível registar o IR auto-hospedado numa nova máquina quando get_LoopbackIpOrName.

**Debug:** Ocorreu um erro de tempo de execução.
O inicializador de tipo para 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' lançou uma exceção.
Um erro não recuperável ocorreu durante uma procura na base de dados.
 
**Detalhe de exceção:** System.TypeInitializationExcepção: O inicializador de tipo para 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' lançou uma exceção. ---> System.Net.SocketException: Ocorreu um erro não recuperável durante uma verificação de base de dados em System.Net.Dns.GetAddrInfo (nome de corda).

#### <a name="cause"></a>Causa

A questão geralmente acontece quando se resolve o local.

#### <a name="resolution"></a>Resolução

Utilize local 127.0.0.1 para hospedar ficheiros e resolver tal problema.


### <a name="self-hosted-setup-failed"></a>A configuração auto-acolessão falhou    

#### <a name="symptoms"></a>Sintomas

Não é possível desinstalar um IR existente, ou instalar um novo IR, ou atualizar um IR existente para um novo IR.

#### <a name="cause"></a>Causa

A instalação depende do serviço de instalação do Windows. Existem razões variantes que podem causar problemas de instalação:
- Não há espaço suficiente em disco
- Falta de permissões
- O serviço NT está bloqueado por alguma razão
- A utilização do CPU é muito alta
- O ficheiro MSI está hospedado numa localização de rede lenta
- Alguns ficheiros ou registos do sistema foram tocados involuntariamente


## <a name="self-hosted-ir-connectivity-issues"></a>Problemas de conectividade ir auto-hospedados

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>O tempo de integração auto-hospedado não pode ligar-se ao serviço na nuvem

#### <a name="symptoms"></a>Sintomas

![Problema de conexão IV auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O tempo de integração auto-hospedado não pode ligar-se ao serviço Data Factory (backend). Este problema é normalmente causado por definições de rede na firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de execução de integração está em execução.
    
   ![Estado de funcionamento do serviço de IR auto-hospedado](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se o serviço estiver funcionando, vá para o passo 3.

1. Se não houver procuração configurada no tempo de funcionamento da integração auto-hospedada (que é a definição padrão), executar o seguinte comando PowerShell na máquina onde o tempo de execução de integração auto-hospedado é instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > O URL de serviço pode variar, dependendo da localização da sua Data Factory. Pode encontrar o URL de serviço em **ADF UI**  >  **Connections**  >  **Integrationtimes**  >  EditE URLs de Serviço de Ver Urls de serviço de imposições **auto-hospedadas**  >  **Nodes**  >  **View Service URLs** .
            
    Segue-se a resposta esperada:
            
    ![Resposta ao comando PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se não receber a resposta esperada, utilize um dos seguintes métodos adequados à sua situação:
            
    * Se receber uma mensagem "Nome remoto não pode ser resolvido", existe um problema do Sistema de Nome de Domínio (DNS). Contacte a sua equipa de rede para corrigir este problema.
    * Se receber uma mensagem "ssl/tls cert não é confiável", verifique se o certificado para https://wu2.frontend.clouddatahub.net/ se confiar na máquina e, em seguida, instale o certificado público utilizando o Certificate Manager. Esta ação deve atenuar a questão.
    * Aceda ao visualizador **do Windows**  >  **Event (registos)**  >  **Aplicações e serviços**  >  **Integração Desacurecida** e verifique se há falhas causadas por DNS, uma regra de firewall ou definições de rede da empresa. (Se encontrar tal falha, feche à força a ligação.) Como todas as empresas têm configurações de rede personalizadas, contacte a sua equipa de rede para resolver estes problemas.

1. Se o "proxy" tiver sido configurado no tempo de funcionação da integração auto-hospedada, verifique se o seu servidor proxy pode aceder ao ponto final do serviço. Para obter um comando de amostra, consulte [PowerShell, pedidos web e proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

Segue-se a resposta esperada:
            
![Resposta ao comando Powershell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações de procuração:
> *    Verifique se o servidor proxy precisa de ser colocado na lista de destinatários seguros. Em caso afirmativo, certifique-se de que [estes domínios](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) estão na lista de destinatários seguros.
> *    Verifique se o certificado TLS/SSL "wu2.frontend.clouddatahub.net/" é fidedigno no servidor proxy.
> *    Se estiver a utilizar a autenticação ative directory no proxy, altere a conta de serviço para a conta de utilizador que possa aceder ao proxy como "Serviço de Tempo de Execução de Integração".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Error message: Self-hosted integration runtime node/ logical SHIR is inative/ "Running (Limited)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um estado **inativo,** como mostra a seguinte imagem:

![Nó iv Self-Hosted inativo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Este comportamento ocorre quando os nós não conseguem comunicar uns com os outros.

#### <a name="resolution"></a>Resolução

1. Faça login no VM hospedado no nó. No tempo de execução de integração de **aplicações e serviços,**  >  **Integration Runtime** abra o Visualizador de Eventos e filtre todos os registos de erro.

1. Verifique se um registo de erro contém o seguinte erro: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se vir este erro, corram o seguinte numa linha de comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se receber o seguinte erro, contacte o seu departamento de TI para obter ajuda para corrigir este problema. Depois de conseguir fazer telnet com sucesso, contacte o Microsoft Support se ainda tiver problemas com o estado do nó de tempo de execução integrador.
        
   ![Erro da linha de comando](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verifique se o registo de erros contém o seguinte:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Para resolver a questão, experimente um ou ambos os seguintes métodos:
    - Coloque todos os nós no mesmo domínio.
    - Adicione o IP para hospedar o mapeamento em todos os ficheiros anfitriões do VM hospedado.

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>Problema de conectividade entre a Infra-fábrica de infravermelhos auto-acolôda e a fábrica de dados ou o IR auto-alojado e a fonte de dados/pia

Para resolver o problema de conectividade da rede, deve saber recolher os vestígios da rede, entender como usá-lo e [analisar o traço netmon](#how-to-analyze-netmon-trace) antes de aplicar as Ferramentas Netmon em casos reais a partir de IR auto-hospedado.

#### <a name="symptoms"></a>Sintomas

Às vezes, quando resoluçmos os problemas de conectividade, tais como abaixo de um entre o IR auto-hospedado e a Fábrica de Dados: 

![O pedido HTTP falhou](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Ou o que se encontra entre o IR auto-alojado e a fonte de dados/pia, que encontraremos os seguintes erros:

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>Resolução:

Ao encontrar questões acima, consulte as seguintes instruções para resolver mais problemas:

Pegue o traço de netmon e analise mais.
- Em primeiro lugar, pode configurar o filtro para ver qualquer reset do servidor para o lado do cliente. A partir do exemplo abaixo, pode ver o lado do servidor é o servidor Data Factory.

    ![Servidor de fábrica de dados](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- Quando receber o pacote de reset, pode encontrar a conversa seguindo o TCP.

    ![Encontrar conversa](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- Em seguida, pode obter a conversão entre o servidor cliente e o servidor Data Factory abaixo, removendo o filtro.

    ![Obter conversa](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- Com base nos vestígios de netmon recolhidos, podemos dizer que o total de TTL (TimeToLive) é de 64. De acordo com os **Valores limite de TTL e hop predefinidos** mencionados [neste artigo](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) (como extraído abaixo), podemos ver que é o Sistema Linux que reinicia a embalagem e causa a desconexão.

    Os valores padrão de TTL e Limite de Salto variam entre diferentes sistemas operativos, aqui estão os padrão para alguns:
    - Kernel Linux 2.4 (cerca de 2001): 255 para TCP, UDP e ICMP
    - Kernel Linux 4.10 (2015): 64 para TCP, UDP e ICMP
    - Windows XP (2001): 128 para TCP, UDP e ICMP
    - Windows 10 (2015): 128 para TCP, UDP e ICMP
    - Windows Server 2008: 128 para TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 para TCP, UDP e ICMP
    - macOS (2001): 64 para TCP, UDP e ICMP

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    No entanto, é mostrado como 61 em vez de 64 em exemplo, porque quando o pacote de rede chega ao destino, precisa de passar por diferentes lúpulos como routers/dispositivos de rede. O número de routers/dispositivos de rede será deduzido na TTL final.
    Neste caso, podemos ver que o Reset pode ser enviado do Linux System com TTL 64.

- Temos de verificar o quarto salto do Auto-alojado IR para confirmar de onde o dispositivo de reset pode vir.
 
    *Pacote de rede do Linux System A com TTL 64 -> B TTL 64 Menos 1 = 63 -> C TTL 63 Menos 1 = 62 -> TTL 62 Menos 1 = 61 IR auto-hospedado*

- Na situação ideal, o TTL será 128, o que significa que o Windows System está a executar a nossa Fábrica de Dados. Como mostrado a seguir, *128 - 107 = 21 lúpulo* , o que significa que 21 lúpulos para o pacote foram enviados da Data Factory para o AUTO-hospedado IR durante o aperto de mão TCP 3.
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Por isso, tens de envolver a equipa de rede para verificar qual é o quarto salto do IR auto-hospedado. Se for a firewall como Sistema Linux, verifique quaisquer registos sobre o porquê desse dispositivo reiniciar a embalagem após o aperto de mão TCP 3. No entanto, se não tiver a certeza de onde fazer a investigação, tente obter os vestígios de netmon do IR e firewall auto-hospedados durante o tempo problemático para descobrir que dispositivo pode redefinir este pacote e causar a desconexão. Neste caso, também precisa de envolver a sua equipa de rede para seguir em frente.

### <a name="how-to-analyze-netmon-trace"></a>Como analisar vestígios de netmon

> [!NOTE] 
> A instrução abaixo é aplicável ao traço netmon. Uma vez que o traço de netmon está fora de suporte, pode alavancar o tubarão-fio como o mesmo.

Quando se tenta teletar **8.8.8.8.8 888** com vestígios de netmon recolhidos, é suposto ver abaixo o rastreio:

![traço netmon 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![traço netmon 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

Isto significa que não foi possível fazer a ligação TCP ao lado do servidor **8.8.8.8** com base na porta **888** , pelo que vê aí dois pacotes adicionais **SynReTransmit.** Uma vez que a Fonte **SELF-HOST2** não conseguiu estabelecer ligação a **8.8.8.8** no primeiro pacote, continuará a fazer a ligação.

> [!TIP]
> - Pode clicar **Load Filter** em  ->  **endereços de filtro padrão de filtro** de carga  ->  **Addresses**  ->  **endereços IPv4** .
> - Inserir **IPv4.Endereço == 8.8.8.8** como filtro e clique em **Aplicar** . Depois disso, só verá a comunicação da máquina local para o destino **8.8.8.8** .

![endereços de filtro 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![endereços de filtro 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Abaixo o exemplo mostra como seria um bom cenário. 

- Se a Telnet **8.8.8.8 53** estiver a funcionar bem sem qualquer problema, pode ver o aperto de mão TCP 3 acontecer, e a sessão termina com um aperto de mão TCP 4.

    ![bom exemplo de cenário 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![bom exemplo de cenário 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- Com base no aperto de mão TCP 3 acima, pode ver abaixo o fluxo de trabalho:

    ![Fluxo de trabalho de aperto de mão TCP 3](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- O aperto de mão TCP 4 para terminar a sessão e o seu fluxo de trabalho serão mostrados como seguintes:

    ![Aperto de mão TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Fluxo de trabalho de aperto de mão TCP 4](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


### <a name="receiving-email-to-update-the-network-configuration-to-allow-communication-with-new-ip-addresses"></a>Receber e-mail para atualizar a configuração da rede para permitir a comunicação com novos endereços IP

#### <a name="email-notification-from-microsoft"></a>Notificação de e-mail da Microsoft

Pode receber abaixo a notificação por e-mail, que recomenda que atualize a configuração da rede para permitir a comunicação com novos endereços IP para Azure Data Factory até 8 de novembro de 2020:

   ![Notificação por e-mail](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="how-to-determine-if-you-are-impacted-by-this-notification"></a>Como determinar se é afetado por esta notificação

Esta notificação tem impacto nos seguintes cenários:
##### <a name="scenario-1-outbound-communication-from-self-hosted-integration-runtime-running-on-premises-behind-the-corporate-firewall"></a>Cenário 1: Comunicação de saída do Self-hosted Integration Runtime a decorrer nas instalações por trás do firewall corporativo
Como determinar se é impactado:
- Não é afetado se estiver a definir regras de firewall com base em nomes FQDN utilizando a abordagem descrita neste documento: [configuração de firewall e permitir a configuração da lista para endereço ip](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-address-of-gateway).
- No entanto, é impactado se estiver a ativar explicitamente a lista de permitir iPs de saída na sua firewall corporativa.

Ação a tomar se tiver impacto: notifique a sua equipa de infraestruturas de rede para atualizar a sua configuração de rede para utilizar os mais recentes endereços IP da Data Factory até 8 de novembro de 2020.  Para descarregar os mais recentes endereços IP, aceda ao [link de descarregamento do intervalo de descarregamento de tags de serviço.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

##### <a name="scenario-2-outbound-communication-from-self-hosted-integration-runtime-running-on-an-azure-vm-inside-customer-managed-azure-virtual-network"></a>Cenário 2: Comunicação de saída do Self-hosted Integration Runtime em execução em um Azure VM dentro do cliente gerido rede virtual Azure
Como determinar se é impactado:
- Verifique se tem alguma regra NSG de saída na sua rede privada que contenha tempo de execução de integração auto-hospedado. Se não houver restrições de saída, então não há impacto.
- Se tiver restrições de regras de saída, verifique se utiliza ou não a etiqueta de serviço. Se utilizar a etiqueta de serviço, então não precisa de alterar ou adicionar nada, uma vez que as novas gamas IP estão na etiqueta de serviço existente. 
 ![Verificação de destino](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)
- No entanto, é impactado se estiver a ativar explicitamente a lista de autorizações para endereços IP de saída na definição de regras NSG na rede virtual Azure.

Ação a tomar se tiver impacto: notifique a sua equipa de infraestruturas de rede para atualizar as regras NSG na sua configuração de rede virtual Azure para utilizar os mais recentes endereços IP da Data Factory até 8 de novembro de 2020.  Para descarregar os mais recentes endereços IP, aceda ao [link de descarregamento do intervalo de descarregamento de tags de serviço.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-customer-managed-azure-virtual-network"></a>Cenário 3: Comunicação de saída do SSIS Integration Runtime na rede virtual Azure gerida pelo cliente
- Verifique se tem alguma regra NSG de saída na sua rede privada que contenha tempo de execução de integração SSIS. Se não houver restrições de saída, então não há impacto.
- Se tiver restrições de regras de saída, verifique se utiliza ou não a etiqueta de serviço. Se utilizar a etiqueta de serviço, então não precisa de alterar ou adicionar nada, uma vez que as novas gamas IP estão na etiqueta de serviço existente.
- No entanto, é impactado se estiver a ativar explicitamente a lista de autorizações para endereço IP de saída na definição de regras NSG na rede virtual Azure.

Ação a tomar se tiver impacto: notifique a sua equipa de infraestruturas de rede para atualizar as regras NSG na sua configuração de rede virtual Azure para utilizar os mais recentes endereços IP da Data Factory até 8 de novembro de 2020.  Para descarregar os mais recentes endereços IP, aceda ao [link de descarregamento do intervalo de descarregamento de tags de serviço.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

### <a name="could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Não foi possível estabelecer uma relação de confiança para o canal seguro SSLTLS 

#### <a name="symptoms"></a>Sintomas

O auto-alojado IR não conseguiu ligar-se ao serviço da ADF.

Ao verificar o registo de eventos SHIR ou os registos de notificação do cliente na tabela CustomLogEvent, encontrar-se-á a seguinte mensagem de erro:

`The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.The remote certificate is invalid according to the validation procedure.`

Como verificar o certificado de servidor do serviço ADF:

A forma mais simples é abrir o URL de serviço ADF no navegador, por exemplo, abrir https://eu.frontend.clouddatahub.net/ na máquina onde o SHIR está instalado e, em seguida, ver as informações do certificado do servidor:

  ![Verifique o certificado do servidor do serviço ADF](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Verifique o caminho do certificado do servidor](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Causa

Duas razões possíveis para esta questão:

- O certificado de servidor de serviço Root CA da ADF não é fidedigno na máquina onde o SHIR está instalado. 
- Está a utilizar procuração no seu ambiente e o certificado de servidor do serviço ADF é substituído pelo representante, enquanto o certificado de servidor substituído não é fidedigno pela máquina onde o SHIR está instalado.

#### <a name="solution"></a>Solução

- Por razão 1, certifique-se de que o certificado do servidor ADF e a sua cadeia de certificados são fidedignos pela máquina onde o SHIR está instalado.
- Por razão 2, confie na raiz substituída ca na máquina SHIR, ou configuure o representante para não substituir o certificado de servidor ADF.

Consulte [este artigo](https://docs.microsoft.com/skype-sdk/sdn/articles/installing-the-trusted-root-certificate) para obter mais informações sobre um certificado no Windows.

#### <a name="additional-info"></a>Informações adicionais
Estamos a lançar um novo certificado SSL, assinado pela DigiCert, por favor, verifique se o DigiCert Global Root G2 está na raiz de confiança CA.

  ![Raiz Global DigiCert G2](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Caso contrário, descarregue-o a partir [daqui.](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ) 

## <a name="self-hosted-ir-sharing"></a>Partilha do IR Autoalojado

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Share AUTO-hospedado IR de um inquilino diferente não é apoiado 

#### <a name="symptoms"></a>Sintomas

Você pode notar outras fábricas de dados (em diferentes inquilinos) enquanto tenta partilhar o IR auto-hospedado da Azure Data Factory UI, mas não pode compartilhar o IR auto-hospedado em fábricas de dados que estão em diferentes inquilinos.

#### <a name="cause"></a>Causa

O IR auto-hospedado não pode ser partilhado entre inquilinos cruzados.


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)