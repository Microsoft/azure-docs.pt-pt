---
title: Resolução de problemas de integração auto-acolagem na Azure Data Factory
description: Saiba como resolver problemas de integração auto-hospedados na Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 2cb0e0870b32270340e37d54dc54a43b22ee014a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376467"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Resolver problemas do runtime de integração autoalojado

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para o tempo de execução de integração auto-hospedado (IR) na Azure Data Factory.

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Recolha de registos de IR auto-hospedados da Azure Data Factory

Para atividades falhadas que estão a decorrer num IR auto-hospedado ou num IR partilhado, a Azure Data Factory suporta a visualização e o upload de registos de erros. Para obter o ID do relatório de erro, siga as instruções aqui e, em seguida, insira o ID do relatório para procurar por questões conhecidas relacionadas.

1. Na Fábrica de Dados, selecione **Pipeline runs**.

1. Em **Atividade executa**, na coluna **Erro,** selecione o botão realçado para exibir os registos de atividade, como mostra o seguinte screenshot:

    ![Screenshot da secção "Activity runs" no painel "All pipeline runs".](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    Os registos de atividade são apresentados para a execução da atividade falhada.

    ![Screenshot dos registos de atividade para a atividade falhada.](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. Para obter mais assistência, selecione **Enviar registos**.
 
   O **Share os registos de execução de integração auto-hospedados (IR) com** a janela da Microsoft abre.

    ![Screenshot da janela "Partilhar o tempo de execução de integração auto-hospedado (IR) com a Microsoft".](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. Selecione quais os registos que deseja enviar. 
    * Para um *IR auto-hospedado,* pode carregar registos relacionados com a atividade falhada ou todos os registos no nó IR auto-hospedado. 
    * Para um *IR partilhado,* é possível fazer o upload de apenas registos relacionados com a atividade falhada.

1. Quando os registos estiverem carregados, mantenha um registo do ID do Relatório para utilização posterior se precisar de mais assistência para resolver o problema.

    ![Screenshot do ID do relatório apresentado na janela de progresso do upload para os registos de INFRAVERMELHOS.](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> Os pedidos de visualização e upload de registos são executados em todas as instâncias ir auto-hospedadas online. Se houver registos em falta, certifique-se de que todos os casos de IR auto-hospedados estão online. 


## <a name="self-hosted-ir-general-failure-or-error"></a>Erro ou falha geral do IR Autoalojado

### <a name="out-of-memory-issue"></a>Fora da questão da memória

#### <a name="symptoms"></a>Sintomas

Um erro OutOfMemoryException (OOM) ocorre quando tenta executar uma atividade de procura com um IR ligado ou um IR auto-hospedado.

#### <a name="cause"></a>Causa

Uma nova atividade pode lançar um erro OOM se a máquina de infravermelhos experimentar uma utilização momentânea de alta memória. O problema pode ser causado por um grande volume de atividade simultânea, e o erro é por design.

#### <a name="resolution"></a>Resolução

Verifique a utilização do recurso e a execução simultânea da atividade no nó DE INFRAVERMELHO. Ajuste o tempo de atividade interno e desencadeado para evitar demasiada execução num único nó de infravermelhos ao mesmo tempo.

### <a name="concurrent-jobs-limit-issue"></a>Problema de limite de trabalhos simultâneos

#### <a name="symptoms"></a>Sintomas

Quando se tenta aumentar o limite de postos de trabalho simultâneos a partir da interface Azure Data Factory, o processo está em situação *de Atualização.*

Cenário de exemplo: O valor máximo de empregos simultâneos está atualmente definido para 24, e quer aumentar a contagem para que os seus empregos possam correr mais rapidamente. O valor mínimo que pode introduzir é 3, e o valor máximo é de 32. Aumenta o valor de 24 para 32 e, em seguida, seleciona o botão **'Actualização'.** O processo fica preso no estado *de Atualização,* como mostra a imagem seguinte. Refresque a página e o valor ainda é exibido como 24. Não foi atualizado para 32 como esperava.

![Screenshot do painel de nodes do tempo de execução da integração, exibindo o processo preso no estado de "Atualização".](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>Causa

O limite do número de empregos simultâneos depende do núcleo e memória lógicos do computador. Tente ajustar o valor para baixo para um valor como 24, e depois ver o resultado.

> [!TIP] 
> -    Para saber mais sobre a contagem de núcleos lógicos e para determinar a contagem de núcleos lógicos da sua máquina, consulte [quatro formas de encontrar o número de núcleos no seu CPU no Windows 10](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/).
> -    Para aprender a calcular a matemática.log, vá à [calculadora logaritm.](https://www.rapidtables.com/calc/math/Log_Calculator.html)


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>Emissão de certificado SSL auto-hospedado de alta disponibilidade (HA)

#### <a name="symptoms"></a>Sintomas

O nó de trabalho de ir auto-alojado reportou o seguinte erro:

"Falhou em retirar estados partilhados do nó primário net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. ID de atividade: XXXXX O certificado X.509 CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building falhou. O certificado utilizado tem uma cadeia de confiança que não pode ser verificada. Substitua o certificado ou altere o certificadoMode de assinatura. A função de revogação não foi capaz de verificar a revogação porque o servidor de revogação estava offline."

#### <a name="cause"></a>Causa

Quando lida com casos relacionados com um aperto de mão SSL/TLS, poderá encontrar alguns problemas relacionados com a verificação da cadeia de certificados. 

#### <a name="resolution"></a>Resolução

- Aqui está uma forma rápida e intuitiva de resolver uma falha de construção da cadeia de certificados X.509:
 
    1. Exporte o certificado, que precisa de ser verificado. Para tal, faça o seguinte:
    
       a. No Windows, selecione **Iniciar**, comece a digitar **certificados** e, em seguida, selecione **Gerir certificados de computador**.
       
       b. No File Explorer, no painel esquerdo, procure o certificado que pretende verificar, clique com o botão direito e, em seguida, selecione **Todas as tarefas**  >  **Export**.
    
        ![Screenshot do controlo "Todas as Tarefas" > "Exportação" para um certificado no painel "Gerir certificados de computador".](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. Copie o certificado exportado para a máquina cliente. 
    3. Do lado do cliente, numa janela de aviso de comando, executar o seguinte comando. Certifique-se de substituir *\<certificate path>* e *\<output txt file path>* pelos caminhos reais.
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        Por exemplo:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. Verifique se há erros no ficheiro TXT de saída. Pode encontrar o resumo do erro no final do ficheiro TXT.

        Por exemplo: 

        ![Screenshot de um resumo de erro no final do ficheiro TXT.](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        Se não vir um erro no final do ficheiro de registo, como mostrado na imagem seguinte, pode considerar que a cadeia de certificados foi construída com sucesso na máquina do cliente.
        
        ![Screenshot de um ficheiro de registo não mostrando erros.](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- Se uma extensão de nome de ficheiro AIA (Authority Information Access), CDP (CRL Distribution Point) ou OCSP (Protocolo de Estado do Certificado Online) estiver configurada no ficheiro de certificado, pode verificar de uma forma mais intuitiva:
 
    1. Obtenha estas informações verificando os dados do certificado, como mostrado na imagem seguinte:
    
        ![Screenshot dos detalhes do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. Execute o seguinte comando. Certifique-se de que substitui *\<certificate path>* pelo caminho real do certificado.
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        A ferramenta URL Retrieval abre. 
        
    1. Para verificar certificados com extensões de nome de ficheiroS AIA, CDP e OCSP, selecione **Retrieve**.

        ![Screenshot da ferramenta de recuperação de URL e do botão Recuperar.](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        Construiu a cadeia de certificados com sucesso se o estado do certificado da AIA *for Verificado* e o estado do certificado de CDP ou OCSP *for Verificado*.

        Se falhar quando tentar recuperar a AIA ou CDP, trabalhe com a sua equipa de rede para preparar a máquina do cliente para se ligar ao URL-alvo. Será suficiente se o caminho HTTP ou o caminho do Protocolo de Acesso ao Diretório Leve (LDAP) puderem ser verificados.

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>O IR Autoalojado não conseguiu carregar o ficheiro ou a assemblagem

#### <a name="symptoms"></a>Sintomas

Obtém a seguinte mensagem de erro:

"Não foi possível carregar ficheiro ou montagem 'XXXXXXXXXXXXXX, Versão=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXXX' ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado. ID de atividade: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"
 
Aqui está uma mensagem de erro mais específica: 

"Não foi possível carregar ficheiros ou montagem 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXXX" ou uma das suas dependências. O sistema não consegue encontrar o ficheiro especificado. ID de atividade: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"

#### <a name="cause"></a>Causa

No Monitor de Processos, pode ver o seguinte resultado:

[![Screenshot da lista Paths no Monitor de Processos.](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> No Monitor de Processos, pode definir os filtros como mostrado na imagem seguinte.
>
> A mensagem de erro anterior diz que o DLL System.ValueTuple não está localizado na pasta cache de *montagem global* (GAC) relacionada, na pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway,* ou na pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Shared.*
>
> Basicamente, o processo carrega o DLL primeiro a partir da pasta *GAC,* depois da pasta *Partilhada* e, finalmente, da pasta *Gateway.* Portanto, pode carregar o DLL de qualquer caminho que seja útil.

<br>

![Screenshot da página "Process Monitor Filter", listando os filtros para o DLL.](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>Resolução

Encontrará o ficheiro *System.ValueTuple.dll* na pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan.* Para resolver o problema, copie o ficheiro *System.ValueTuple.dll* para a pasta *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway.*

Pode utilizar o mesmo método para resolver outros problemas de ficheiros ou montagem em falta.

#### <a name="more-information-about-this-issue"></a>Mais informações sobre esta questão

A razão pela qual vê o *System.ValueTuple.dll* em *%windir%\Microsoft.NET\montagem* e *%windir%\montagem* é que este é um comportamento .NET. 

No erro seguinte, pode ver claramente que o *conjunto System.ValueTuple* está em falta. Esta questão surge quando a aplicação tenta verificar a *System.ValueTuple.dll* assembléia.
 
" \<LogProperties> \<ErrorInfo> [{"Code":0,"Mensagem":"O tipo de inicializador de 'Npgsql.PoolManager' lançou uma exceção.""EventType":0,"Categoria":5,"Dados": {} "MsgId":nulo,"ExceptionType":"System.TypeInitializationExcepção","Fonte":"Npgsql","StackTrace":""InnerEventInfos":[{"Code":0,"Mensagem":"Não podia carregar ficheiro ou montagem 'System.ValueTuple, Versão=4.0.2.0, Cultura=neutra, PublicKeyToken=XXXXXXXX' ou uma das suas dependências. O sistema não pode encontrar o ficheiro especificado.""EventType":0,"Categoria":5"Dados": {} "MsgId":nulo,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":""InnerEventInfos":[} \</ErrorInfo> \</LogProperties>
 
Para obter mais informações sobre o GAC, consulte [a Cache de Montagem Global.](/dotnet/framework/app-domains/gac)


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>Falha a chave de autenticação em tempo de execução de integração auto-hospedada

#### <a name="symptoms"></a>Sintomas

O tempo de integração auto-hospedado de repente fica offline sem uma Chave de Autenticação, e o Registo de Eventos apresenta a seguinte mensagem de erro: 

"A Chave de Autenticação ainda não foi atribuída"

![Screenshot do painel de eventos de integração que mostra que a Chave de Autenticação ainda não foi atribuída.](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>Causa

- O nó IR auto-hospedado ou o IR lógico auto-hospedado no portal Azure foi eliminado.
- Foi realizada uma desinstalação limpa.

#### <a name="resolution"></a>Resolução

Se nenhuma das causas anteriores se aplicar, pode ir à pasta *%programdata%\Microsoft\Data Transfer\DataManagementGateway* para ver se o ficheiro *Configurações* foi eliminado. Se foi eliminado, siga as instruções do artigo Netwrix [Detete quem apagou um ficheiro dos seus servidores de ficheiros Windows](https://www.netwrix.com/how_to_detect_who_deleted_file.html).

![Screenshot do painel de detalhes do registo do evento para verificar o ficheiro Configurações.](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>Não pode usar IR auto-hospedado para fazer a ponte de duas lojas de dados no local

#### <a name="symptoms"></a>Sintomas

Depois de criar IRs auto-hospedados tanto para as datas de origem como para destino, pretende ligar as duas IRs para terminar uma atividade de cópia. Se as datastores estiverem configuradas em diferentes redes virtuais, ou se as datastores não entenderem o mecanismo gateway, recebe qualquer um dos seguintes erros: 

* "O condutor da fonte não pode ser encontrado no destino IR"
* "A fonte não pode ser acedida pelo destino IR"
 
#### <a name="cause"></a>Causa

O IR auto-hospedado é projetado como um nó central de uma atividade de cópia, não um agente cliente que precisa ser instalado para cada datastore.
 
Neste caso, deverá criar o serviço ligado para cada datastore com o mesmo IR, e o IR deverá poder aceder a ambos os datastore através da rede. Não importa se o IR está instalado na loja de dados de origem ou na loja de dados de destino, ou numa terceira máquina. Se forem criados dois serviços ligados com IRs diferentes, mas utilizados na mesma atividade de cópia, o destino IR é utilizado, e é necessário instalar os controladores para ambas as datas-lojas na máquina IV de destino.

#### <a name="resolution"></a>Resolução

Instale os controladores tanto para as lojas de dados de origem como de destino no destino IR, e certifique-se de que pode aceder à loja de dados de origem.
 
Se o tráfego não puder passar pela rede entre duas datastores (por exemplo, estão configurados em duas redes virtuais), poderá não terminar a cópia numa só atividade mesmo com o IR instalado. Se não conseguir terminar a cópia numa única atividade, pode criar duas atividades de cópia com duas IRs, cada uma num VENT: 
* Copie um IR da datastore 1 para o armazenamento de blob Azure
* Copie outro IR do Azure Blob Storage para a datastore 2. 

Esta solução poderia simular a obrigatoriedade de usar o IR para criar uma ponte que liga duas datas-lojas desligadas.


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>Problema de sincronização de credenciais causa perda de credencial de HA

#### <a name="symptoms"></a>Sintomas

Se a credencial de fonte de dados "XXXXXXXXX" for eliminada do nó de tempo de execução de integração atual com carga útil, receberá a seguinte mensagem de erro:

"Quando eliminar o serviço de ligação no portal Azure, ou a tarefa tiver a carga útil errada, por favor, crie um novo serviço de ligação com a sua credencial novamente."

#### <a name="cause"></a>Causa

O seu IR auto-hospedado é construído em modo HA com dois nós, mas os nós não estão em um estado de sincronização de credenciais. Isto significa que as credenciais armazenadas no nó do despachante não são sincronizadas com outros nós operários. Se alguma falha acontecer do nó do despachante ao nó do trabalhador, e as credenciais existirem apenas no nó do despacho anterior, a tarefa falhará quando estiver a tentar aceder às credenciais, e receberá o erro anterior.

#### <a name="resolution"></a>Resolução

A única maneira de evitar esta questão é garantir que os dois nós estão em estado de sincronização de credenciais. Se não estiverem sincronizados, tem de reentrar nas credenciais do novo despachante.


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>Não posso escolher o certificado porque falta a chave privada

#### <a name="symptoms"></a>Sintomas

* Importou um ficheiro PFX para a loja de certificados.
* Quando selecionou o certificado através da UI do Gestor de Configuração de INFRAVERMELHOS, recebeu a seguinte mensagem de erro:

   "Falhou em alterar o modo de encriptação de comunicação intranet. É provável que o certificado \<*certificate name*> ' ' pode não ter uma chave privada que seja capaz de troca de chaves ou o processo pode não ter direitos de acesso para a chave privada. Por favor, veja a exceção interior para detalhes.

    ![Screenshot do painel de configuração de configuração de integração, exibindo uma mensagem de erro "chave privada em falta".](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>Causa

- A conta de utilizador tem um baixo nível de privilégio e não consegue aceder à chave privada.
- O certificado foi gerado como uma assinatura, mas não como uma troca de chaves.

#### <a name="resolution"></a>Resolução

* Para operar a UI, utilize uma conta com privilégios adequados para aceder à chave privada.  
* Importar o certificado executando o seguinte comando:
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>Configuração de IR auto-hospedada

### <a name="integration-runtime-registration-error"></a>Erro de registo de tempo de integração 

#### <a name="symptoms"></a>Sintomas

Você pode ocasionalmente querer executar um IR auto-hospedado em uma conta diferente por qualquer uma das seguintes razões:
- A política da empresa não permite a conta de serviço.
- É necessária alguma autenticação.

Depois de alterar a conta de serviço no painel de serviço, poderá descobrir que o tempo de execução da integração deixa de funcionar e obtém a seguinte mensagem de erro:

"O nó de Runtime de Integração (Self-hosted) encontrou um erro durante o registo. Não é possível ligar-se ao Serviço de Anfitrião de Integração (Self-hosted)."

![Screenshot da janela Do Gestor de Configuração de Tempo de Integração, mostrando um erro de registo iv.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>Causa

Muitos recursos são concedidos apenas à conta de serviço. Quando muda a conta de serviço para outra conta, as permissões de todos os recursos dependentes permanecem inalteradas.

#### <a name="resolution"></a>Resolução

Aceda ao registo de eventos de integração para verificar o erro.

![Screenshot do registo do evento de IR, mostrando que ocorreu um erro de tempo de execução.](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* Se o erro no registo de eventos for "UnauthorizedAccessException", faça o seguinte:

    1. Consulte a conta de serviço de logon *DIAHostService* no painel de serviço do Windows.

        ![Screenshot do painel de propriedades da conta de serviço logon.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verifique se a conta de serviço de início de súmula leu/escreveu permissões para a pasta *%programdata%\Microsoft\DataTransfer\DataManagementGateway.*

        - Por predefinição, se a conta de início de serviço não tiver sido alterada, deve ter permissões de leitura/escrita.

            ![Screenshot do painel de permissões de serviço.](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - Se alterou a conta de início de saúde do serviço, atenue o problema fazendo o seguinte:
 
            a. Efetuar uma desinstalação limpa do atual IR auto-hospedado.   
            b. Instale as bits de IR auto-hospedadas.  
            c. Alterar a conta de serviço fazendo o seguinte:  

             i. Vá à pasta de instalação IV auto-hospedada e, em seguida, mude para a pasta *Desaconstrução de Integração da Microsoft\4.0\Pasta partilhada.*  
             ii. Abra uma janela de aviso de comando utilizando privilégios elevados. Substitua *\<user>* e pelo seu próprio nome de utilizador e senha *\<password>* e, em seguida, execute o seguinte comando:   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. Se pretender alterar para a conta Do Sistema Local, certifique-se de utilizar o formato correto para esta conta: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                *Não* utilize este formato:`dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. Opcionalmente, como o Sistema Local tem privilégios mais elevados do que o Administrador, também pode alterá-lo diretamente em "Serviços".  
             v. Pode utilizar um utilizador local/domínio para a conta de início de sísmia do serviço DE IR.            

            d. Registar o tempo de execução da integração.

* Se o erro for "Serviço de Serviço 'Serviço de Verificação de Integração' (DIAHostService) não foi iniciado. Verifique se tem privilégios suficientes para iniciar serviços de sistema", faça o seguinte:

    1. Consulte a conta de serviço de logon *DIAHostService* no painel de serviço do Windows.
    
        ![Screenshot do painel "Log On" para a conta de serviço.](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. Verifique se a conta de serviço de início de sessão tem **log on como uma** permissão de serviço para iniciar o serviço Windows:

        ![Screenshot do painel de propriedades "Iniciar sessão como serviço".](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>Mais informações

Se nenhum dos dois padrões de resolução anteriores se aplicar no seu caso, tente recolher os seguintes registos de eventos do Windows: 
- Registos de aplicações e serviços > tempo de execução da integração
- Aplicação de > de registos do Windows

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>Não se encontra o botão Registar para registar um IR auto-hospedado    

#### <a name="symptoms"></a>Sintomas

Quando regista um IR auto-hospedado, o botão **Registar** não é apresentado no painel de Gestor de Configuração.

![Screenshot do painel de identificação do Gestor de Configuração, mostrando uma mensagem de que o nó de tempo de execução de integração não está registado.](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>Causa

A partir do lançamento do Tempo de Execução de Integração 3.0, o botão **registar** os nós de tempo de execução existentes foi removido para permitir um ambiente mais limpo e seguro. Se um nó tiver sido registado para um tempo de integração, seja on-line ou não, re-registre-o para outro tempo de integração desinstalando o nó anterior e, em seguida, instale e registe o nó.

#### <a name="resolution"></a>Resolução

1. No Painel de Controlo, desinstale o tempo de integração existente.

    > [!IMPORTANT] 
    > No processo seguinte, selecione **Sim**. Não guarde dados durante o processo de desinstalação.

    ![Screenshot do botão "Sim" para eliminar todos os dados do utilizador do tempo de execução da integração.](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Se não tiver o ficheiro MSI do instalador de tempo de integração, vá ao [centro de descarregamento](https://www.microsoft.com/en-sg/download/details.aspx?id=39717) para descarregar o tempo de execução de integração mais recente.
1. Instale o ficheiro MSI e registe o tempo de execução da integração.


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>Incapaz de registar o IR auto-hospedado por causa de local local    

#### <a name="symptoms"></a>Sintomas

Não é possível registar o IR auto-hospedado numa nova máquina quando utilizar get_LoopbackIpOrName.

**Debug:** Ocorreu um erro de tempo de execução.
O inicializador de tipo de “Microsoft.DataTransfer.DIAgentHost.DataSourceCache” gerou uma exceção.
Um erro não recuperável ocorreu durante uma procura na base de dados.
 
**Detalhe de exceção:** System.TypeInitializationExcepção: O inicializador de tipo para 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' lançou uma exceção. ---> System.Net.SocketException: Ocorreu um erro não recuperável durante uma verificação de base de dados em System.Net.Dns.GetAddrInfo (nome de corda).

#### <a name="cause"></a>Causa

A questão geralmente ocorre quando o local está a ser resolvido.

#### <a name="resolution"></a>Resolução

Utilize o endereço IP local 127.0.0.1 para hospedar o ficheiro e resolver o problema.

### <a name="self-hosted-setup-failed"></a>A configuração auto-acolessão falhou    

#### <a name="symptoms"></a>Sintomas

Não é possível desinstalar um IR existente, instalar um novo IR ou atualizar um IR existente para um novo IR.

#### <a name="cause"></a>Causa

A instalação de tempo de funcionamento de integração depende do serviço Do Instalador do Windows. Pode ter problemas de instalação pelas seguintes razões:
- Espaço insuficiente disponível em disco.
- Falta de permissões.
- O serviço Windows NT está bloqueado.
- A utilização do CPU é muito alta.
- O ficheiro MSI está hospedado numa localização de rede lenta.
- Alguns ficheiros ou registos do sistema foram tocados involuntariamente.

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>A conta de serviço de IR não conseguiu obter acesso a certificados

#### <a name="symptoms"></a>Sintomas

Quando instala um IR auto-hospedado através do Microsoft Integration Runtime Configuration Manager, é gerado um certificado com uma autoridade de certificados fidedigna (CA). O certificado não pôde ser aplicado para encriptar a comunicação entre dois nós, e é apresentada a seguinte mensagem de erro: 

"Falhou na alteração do modo de encriptação de comunicação Intranet: Falhou em conceder à conta de serviço de Integração Runtime o acesso ao \<*certificate name*> certificado'. Código de erro 103"

![Screenshot mostrando a mensagem de erro "... Não concedeu acesso ao certificado de conta de serviço de Integração Runtime".](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>Causa

O certificado está a utilizar o armazenamento do principal fornecedor de armazenamento (KSP), que ainda não é suportado. Até à data, o IR auto-alojado suporta apenas o armazenamento do prestador de serviços criptográfico (CSP).

#### <a name="resolution"></a>Resolução

Recomendamos que utilize certificados CSP neste caso.

**Solução 1** 

Para importar o certificado, executar o seguinte comando:

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![Screenshot do comando certutil para importar o certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**Solução 2** 

Para converter o certificado, executar os seguintes comandos:

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

Antes e depois da conversão:

![Screenshot do resultado antes da conversão do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![Screenshot do resultado após a conversão do certificado.](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>Versão de execução de integração auto-hospedada 5.x
Para a atualização para a versão 5.x do tempo de integração auto-hospedado da Azure Data Factory, exigimos **.NET Framework Runtime 4.7.2** ou mais tarde. Na página de download, encontrará links de descarregamento para a versão 4.x mais recente e as duas versões mais recentes de 5.x. 

Para clientes Azure Data Factory v2:
- Se a atualização automática estiver a decorrer e já tiver atualizado o seu Prazo de Execução .NET Framework para 4.7.2 ou mais tarde, o tempo de funcionamento de integração auto-hospedado será automaticamente atualizado para a versão mais recente 5.x.
- Se a atualização automática estiver a decorrer e não tiver atualizado o seu Prazo de Execução .NET Framework para 4.7.2 ou posterior, o tempo de funcionamento da integração auto-hospedada não será automaticamente atualizado para a versão mais recente 5.x. O tempo de integração auto-hospedado permanecerá na versão atual 4.x. Pode ver um aviso para uma atualização de prazo de execução .NET framework no portal e para o cliente de execução de integração auto-hospedado.
- Se a atualização automática estiver desligada e já tiver atualizado o seu Prazo de Execução .NET Framework para 4.7.2 ou mais tarde, pode descarregar manualmente os 5.x mais recentes e instalá-lo na sua máquina.
- Se a atualização automática estiver desligada e não tiver atualizado o seu Prazo de Execução .NET Framework para 4.7.2 ou mais tarde. Quando tentar instalar manualmente o tempo de execução de integração auto-hospedado 5.x e registar a tecla, será necessário atualizar primeiro a sua versão .NET Framework Runtime.


Para clientes Azure Data Factory v1:
- O tempo de integração auto-hospedado 5.X não suporta a Azure Data Factory v1.
- O tempo de integração auto-hospedado será automaticamente atualizado para a versão mais recente do 4.x. E a última versão de 4.x não expirará. 
- Se tentar instalar manualmente o tempo de execução de integração auto-hospedado 5.x e registar a chave, será notificado de que o tempo de execução de integração auto-hospedado 5.x não suporta o Azure Data Factory v1.


## <a name="self-hosted-ir-connectivity-issues"></a>Problemas de conectividade ir auto-hospedados

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>O tempo de integração auto-hospedado não pode ligar-se ao serviço de nuvem

#### <a name="symptoms"></a>Sintomas

Quando tenta registar o tempo de execução de integração auto-hospedado, o Gestor de Configuração apresenta a seguinte mensagem de erro:

"O nó de runtime de integração (auto-hospedado) encontrou um erro durante o registo."

![Screenshot do nó "O tempo de execução de integração (auto-hospedado) encontrou um erro durante o registo".](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>Causa 

O IR auto-alojado não pode ligar-se ao serviço Azure Data Factory. Este problema é geralmente causado por definições de rede na firewall.

#### <a name="resolution"></a>Resolução

1. Verifique se o serviço de execução de integração está em execução. Se for, vá para o passo 2.
    
   ![Screenshot mostrando que o serviço de IR auto-hospedado está em execução.](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. Se não for configurado nenhum proxy no IR auto-hospedado, que é a definição predefinida, executar o seguinte comando PowerShell na máquina onde o tempo de execução de integração auto-alojado está instalado:

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > O URL de serviço pode variar, dependendo da localização da sua instância de fábrica de dados. Para encontrar o URL de serviço, selecione **ADF UI**  >  **Connections**  >  **Integrationtimes**  >  EditE URLs de Serviço de Ver UrLs de serviço **auto-hospedados**  >    >  .
            
    Segue-se a resposta esperada:
            
    ![Screenshot da resposta do comando PowerShell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. Se não receber a resposta que esperava, use um dos seguintes métodos, conforme apropriado:
            
    * Se receber uma mensagem "Nome remoto não pode ser resolvido", existe um problema do Sistema de Nome de Domínio (DNS). Contacte a sua equipa de rede para corrigir o problema.
    * Se receber uma mensagem "ssl/tls cert não é confiável", [verifique o certificado](https://wu2.frontend.clouddatahub.net/) para ver se é de confiança na máquina e, em seguida, instale o certificado público utilizando o Certificate Manager. Esta ação deve atenuar a questão.
    * Aceda ao visualizador **do Windows**  >  **Event (registos)**  >  **Applications and Services**  >  **IntegrationTime**, e verifique se há qualquer falha causada pelo DNS, uma regra de firewall ou definições de rede da empresa. Se encontrar tal falha, feche à força a ligação. Como cada empresa tem as suas próprias definições de rede personalizadas, contacte a sua equipa de rede para resolver estes problemas.

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
            
![Screenshot da esperada resposta do comando Powershell.](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> Considerações de procuração:
> * Verifique se o servidor proxy precisa de ser colocado na lista de destinatários seguros. Em caso afirmativo, certifique-se de que [estes domínios](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network) estão na lista de destinatários seguros.
> * Verifique se o certificado SSL/TLS "wu2.frontend.clouddatahub.net/" é fidedigno no servidor proxy.
> * Se estiver a utilizar a autenticação ative directory no proxy, altere a conta de serviço para a conta de utilizador que possa aceder ao proxy como "Serviço de Tempo de Execução de Integração".

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>Error message: Self-hosted integration runtime node/logical self-hosted IR is inative/ "Running (Limited)"

#### <a name="cause"></a>Causa 

O nó de tempo de execução integrado auto-hospedado pode ter um estatuto de **Inativo,** como mostra a seguinte imagem:

![Screenshot do nó de tempo de execução integrado auto-hospedado com estatuto inativo](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

Este comportamento ocorre quando os nós não conseguem comunicar uns com os outros.

#### <a name="resolution"></a>Resolução

1. Faça login na máquina virtual do nó (VM). No tempo de execução de integração de **aplicações e serviços,**  >  abra o Visualizador de Eventos e filtre os registos de erro.

1. Verifique se um registo de erro contém o seguinte erro: 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. Se vir este erro, executar o seguinte comando numa janela de pedido de comando: 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. Se receber o erro da linha de comando "Não pode abrir a ligação ao anfitrião" que é mostrado na imagem seguinte, contacte o seu departamento de TI para obter ajuda para corrigir este problema. Depois de conseguir fazer telnet com sucesso, contacte o Microsoft Support se ainda tiver problemas com o estado do nó de tempo de execução de integração.
        
   ![Screenshot do erro da linha de comando "Não foi possível abrir a ligação ao anfitrião".](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. Verifique se o registo de erro contém a seguinte entrada:

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. Para resolver a questão, experimente um ou ambos os seguintes métodos:
    - Coloque todos os nós no mesmo domínio.
    - Adicione o IP para hospedar o mapeamento em todos os ficheiros anfitriões do VM hospedado.

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>Problema de conectividade entre o IR auto-hospedado e a sua instância de fábrica de dados ou o IR auto-hospedado e a fonte de dados ou pia

Para resolver o problema de conectividade da rede, deve saber recolher os vestígios de rede, entender como usá-lo e [analisar os vestígios do Microsoft Network Monitor (Netmon)](#analyze-the-netmon-trace) antes de aplicar as Ferramentas Netmon em casos reais a partir do IR auto-hospedado.

#### <a name="symptoms"></a>Sintomas

Pode ocasionalmente precisar de resolver determinados problemas de conectividade entre o IR auto-hospedado e a sua instância de fábrica de dados, como mostrado na imagem seguinte, ou entre o IR auto-hospedado e a fonte de dados ou o afundanço. 

![Screenshot de uma mensagem "Processed HTTP request failed"](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

Em qualquer dos casos, poderá encontrar os seguintes erros:

* "Cópia falhou com erro:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Can connect to SQL Server: 'Address IP'"

* "Ocorreram um ou mais erros. Ocorreu um erro ao enviar o pedido. A ligação subjacente foi encerrada: Ocorreu um erro inesperado numa receção. Não foi possível ler os dados da ligação de transporte: Uma ligação existente foi fechada à força pelo hospedeiro remoto. Uma ligação existente foi fechada à força pelo ID de atividade do hospedeiro remoto."

#### <a name="resolution"></a>Resolução

Quando encontrar os erros anteriores, resolva-os seguindo as instruções desta secção.

- Recolher um vestígio netmon para análise: 

    1. Pode configurar o filtro para ver um reset do servidor para o lado do cliente. Na imagem de exemplo a seguir, pode ver que o lado do servidor é o servidor Data Factory.

        ![Screenshot do servidor da fábrica de dados.](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. Quando receber o pacote de reset, pode encontrar a conversação seguindo o Protocolo de Controlo de Transmissão (TCP).

        ![Screenshot da conversa da TCP.](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. Obtenha a conversação entre o cliente e o servidor Data Factory abaixo removendo o filtro.

        ![Screenshot dos detalhes da conversa.](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- Uma análise do traço Netmon que recolheu mostra que o total do Tempo para Viver (TTL) é de 64. De acordo com os valores mencionados no artigo [IP Time to Live (TTL) e Hop Limit Basics, extraídos](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/) na seguinte lista, pode ver que é o Sistema Linux que reinicia o pacote e causa a desconexão.

    Os valores predefinidos de TTL e Limite de Salto variam entre diferentes sistemas operativos, conforme listado aqui:
    - Kernel Linux 2.4 (cerca de 2001): 255 para TCP, Protocolo de Datagrama do Utilizador (UDP) e Protocolo de Mensagens de Controlo de Internet (ICMP)
    - Kernel Linux 4.10 (2015): 64 para TCP, UDP e ICMP
    - Windows XP (2001): 128 para TCP, UDP e ICMP
    - Windows 10 (2015): 128 para TCP, UDP e ICMP
    - Windows Server 2008: 128 para TCP, UDP e ICMP
    - Windows Server 2019 (2018): 128 para TCP, UDP e ICMP
    - macOS (2001): 64 para TCP, UDP e ICMP

    ![Screenshot mostrando um valor TTL de 61.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    No exemplo anterior, o TTL é mostrado como 61 em vez de 64, porque quando o pacote de rede chega ao seu destino, precisa de passar por vários lúpulo, como routers ou dispositivos de rede. O número de routers ou dispositivos de rede é deduzido para produzir o TTL final.
    
    Neste caso, pode ver que pode ser enviado um reset do Sistema Linux com TTL 64.

-  Para confirmar de onde o dispositivo de reset pode vir, verifique o quarto salto do IR auto-hospedado.
 
    *Pacote de rede do Linux System A com TTL 64 -> B TTL 64 menos 1 = 63 -> C TTL 63 menos 1 = 62 -> TTL 62 menos 1 = 61 IR auto-hospedado*

- Numa situação ideal, o número de lúpulo TTL seria 128, o que significa que o sistema operativo Windows está a executar a sua instância de fábrica de dados. Como mostrado no exemplo seguinte, *128 menos 107 = 21 lúpulo*, o que significa que 21 lúpulos para o pacote foram enviados da instância da fábrica de dados para o auto-alojado IR durante o aperto de mão TCP 3.
 
    ![Screenshot mostrando um valor TTL de 107.](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    Por isso, é necessário envolver a equipa de rede para ver qual é o quarto salto do IR auto-hospedado. Se for a firewall, como com o Sistema Linux, verifique quaisquer registos para ver por que razão esse dispositivo reinicia a embalagem após o aperto de mão TCP 3. 
    
    Se não tem certeza de onde investigar, tente obter o rasto de Netmon do AUTO-alojado IR e da firewall durante o tempo problemático. Esta abordagem irá ajudá-lo a descobrir que dispositivo pode ter reiniciado a embalagem e causado a desconexão. Neste caso, também precisa de envolver a sua equipa de rede para seguir em frente.

### <a name="analyze-the-netmon-trace"></a>Analise o traço Netmon

> [!NOTE] 
> As seguintes instruções aplicam-se ao vestígio de Netmon. Como o vestígio da Netmon está atualmente sem suporte, podes usar o Wireshark para este fim.

Quando tentar teletar **8.8.8.8.8 888** com os vestígios netmon recolhidos, deve ver o traço nas seguintes imagens:

![Screenshot mostrando "Não podia abrir a ligação ao anfitrião na porta 888" mensagem de erro.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Imagem mostrando uma descrição do vestígio de Netmon.](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

As imagens anteriores mostram que não foi capaz de fazer uma ligação TCP ao lado do servidor **8.8.8.8** na porta **888**, pelo que vê dois pacotes adicionais **synReTransmit** lá. Como a fonte **SELF-HOST2** não conseguiu ligar-se a **8.8.8.8** com o primeiro pacote, continuará a tentar fazer a ligação.

> [!TIP]
> Para eso mesmo, experimente a seguinte solução:
> 1. Selecione **endereços**  >  **de filtro padrão de filtro** de  >  **carga**  >  **endereços IPv4**.
> 1. Para aplicar o filtro, insira **o IPv4.Address == 8.8.8.8**, e, em seguida, selecione **Aplicar**. Em seguida, deverá consultar a comunicação da máquina local para o destino **8.8.8.8**.

![Screenshot mostrando endereços de filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![Screenshot mostrando mais endereços de filtro.](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

Os cenários de sucesso são mostrados nos seguintes exemplos: 

- Se conseguir telnet **8.8.8.8.53** sem qualquer problema, há um aperto de mão TCP 3 bem sucedido, e a sessão termina com um aperto de mão TCP 4.

    ![Screenshot mostrando um cenário de conexão bem-sucedido.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![Screenshot mostrando detalhes de um cenário de conexão bem-sucedido.](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- O anterior aperto de mão TCP 3 produz o seguinte fluxo de trabalho:

    ![Diagrama de um fluxo de trabalho de aperto de mão TCP 3.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- O aperto de mão TCP 4 para terminar a sessão é ilustrado pelos seguintes fluxos de trabalho:

    ![Screenshot de detalhes do aperto de mão TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![Diagrama de um fluxo de trabalho de aperto de mão TCP 4.](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>Notificação de e-mail da Microsoft sobre a atualização da configuração da sua rede

Poderá receber a seguinte notificação por e-mail, que recomenda que atualize a sua configuração de rede para permitir a comunicação com novos endereços IP para Azure Data Factory até 8 de novembro de 2020:

   ![Screenshot da notificação de e-mail da Microsoft solicitando atualização da configuração da rede.](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>Determine se esta notificação o afeta

Esta notificação aplica-se aos seguintes cenários:

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>Cenário 1: Comunicação de saída de um tempo de integração auto-hospedado que está a funcionar no local atrás de uma firewall corporativa

Como determinar se está afetado:

- *Não é* afetado se estiver a definir regras de firewall com base em nomes de domínio totalmente qualificados (FQDNs) que utilizam a abordagem descrita na [configuração de uma firewall e permitem a lista de endereços IP](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses).

- Você *é* afetado se você está explicitamente ativando a lista de permitir iPs de saída na sua firewall corporativa.

   Se for afetado, tome as seguintes medidas: até 8 de novembro de 2020, notifique a sua equipa de infraestruturas de rede para atualizar a sua configuração de rede para utilizar os mais recentes endereços IP da fábrica de dados. Para descarregar os mais recentes endereços IP, aceda a [tags de serviço Discover utilizando ficheiros JSON descarregados.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>Cenário 2: Comunicação de saída a partir de um tempo de integração auto-hospedado que está a decorrer num VM Azure dentro de uma rede virtual Azure gerida pelo cliente

Como determinar se está afetado:

- Verifique se tem alguma regra de grupo de segurança de rede de saída (NSG) numa rede privada que contenha tempo de integração auto-hospedado. Se não houver restrições de saída, não é afetado.

- Se tiver restrições de regras de saída, verifique se está a usar etiquetas de serviço. Se estiver a usar etiquetas de serviço, não está afetado. Não há necessidade de alterar ou adicionar nada, porque a nova gama IP está sob as suas etiquetas de serviço existentes. 

  ![Screenshot de uma verificação de destino mostrando DataFactory como o destino.](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Você *é* afetado se estiver a ativar explicitamente a lista de autorizações para endereços IP de saída na definição de regras NSG na rede virtual Azure.

   Se for afetado, tome as seguintes medidas: até 8 de novembro de 2020, notifique a sua equipa de infraestruturas de rede para atualizar as regras NSG na sua configuração de rede virtual Azure para utilizar os mais recentes endereços IP da fábrica de dados. Para descarregar os mais recentes endereços IP, aceda a [tags de serviço Discover utilizando ficheiros JSON descarregados.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>Cenário 3: Comunicação de saída do SSIS Integration Runtime numa rede virtual Azure gerida pelo cliente

Como determinar se está afetado:

- Verifique se tem alguma regra NSG de saída numa rede privada que contenha o tempo de integração dos Serviços de Integração do Servidor SQL (SSIS). Se não houver restrições de saída, não é afetado.

- Se tiver restrições de regras de saída, verifique se está a usar etiquetas de serviço. Se estiver a usar etiquetas de serviço, não está afetado. Não há necessidade de alterar ou adicionar nada porque a nova gama IP está sob as suas etiquetas de serviço existentes.

- Você *é* afetado se estiver a ativar explicitamente a lista de autorizações para endereços IP de saída na definição de regras NSG na rede virtual Azure.

  Se for afetado, tome as seguintes medidas: até 8 de novembro de 2020, notifique a sua equipa de infraestruturas de rede para atualizar as regras NSG na sua configuração de rede virtual Azure para utilizar os mais recentes endereços IP da fábrica de dados. Para descarregar os mais recentes endereços IP, aceda a [tags de serviço Discover utilizando ficheiros JSON descarregados.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>Não foi consegui estabelecer uma relação de confiança para o canal seguro SSL/TLS 

#### <a name="symptoms"></a>Sintomas

O AUTO-alojado IR não conseguiu ligar-se ao serviço da Azure Data Factory.

Quando verificar o registo de eventos ir auto-hospedados ou os registos de notificação do cliente na tabela CustomLogEvent, encontrará a seguinte mensagem de erro:

"A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS. O certificado remoto é inválido de acordo com o procedimento de validação."

A forma mais simples de verificar o certificado de servidor do serviço Data Factory é abrir o URL de serviço da Data Factory no seu navegador. Por exemplo, abra o link do [certificado do servidor de verificação](https://eu.frontend.clouddatahub.net/) na máquina onde o IR auto-hospedado está instalado e, em seguida, veja as informações do certificado do servidor.

  ![Screenshot do painel de certificado do servidor de verificação do serviço Azure Data Factory.](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![Screenshot da janela para verificar o caminho de certificação do servidor.](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>Causa

Há duas razões possíveis para esta questão:

- Razão 1: A raiz ca do certificado de servidor de serviço data factory não é confiável na máquina onde o IR auto-alojado está instalado. 
- Razão 2: Está a utilizar um representante no seu ambiente, o certificado de servidor do serviço Data Factory é substituído pelo representante, e o certificado de servidor substituído não é confiável pela máquina onde o IR auto-alojado está instalado.

#### <a name="resolution"></a>Resolução

- Por motivo 1: Certifique-se de que o certificado do servidor Data Factory e a sua cadeia de certificados são fidedignos pela máquina onde está instalado o IR auto-alojado.
- Pela razão 2: Confie na raiz substituída ca na máquina de INFRAVERMELHO auto-hospedada, ou configuure o representante para não substituir o certificado de servidor data Factory.

Para obter mais informações sobre certificados de confiança no Windows, consulte [instalar o certificado raiz fidedigno](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate).

#### <a name="additional-information"></a>Informações adicionais
Lançámos um novo certificado SSL, assinado pela DigiCert. Verifique se o DigiCert Global Root G2 está na raiz confiável CA.

  ![Screenshot mostrando a pasta DigiCert Global Root G2 no diretório das Autoridades de Certificação de Raiz Fidedigna.](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

Se não estiver na raiz de confiança, [descarregue-a aqui.](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt ) 


## <a name="self-hosted-ir-sharing"></a>Partilha do IR Autoalojado

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>Partilhar um IR auto-hospedado de um inquilino diferente não é apoiado 

#### <a name="symptoms"></a>Sintomas

Você pode notar outras fábricas de dados (em diferentes inquilinos) como você está tentando compartilhar o IR auto-hospedado da UI da Azure Data Factory, mas você não pode compará-lo em fábricas de dados que estão em diferentes inquilinos.

#### <a name="cause"></a>Causa

O IR auto-alojado não pode ser partilhado entre inquilinos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, experimente os seguintes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página do Microsoft Q&A](/answers/topics/azure-data-factory.html)
*  [Stack overflow forum para data factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento](concepts-data-flow-performance.md)
