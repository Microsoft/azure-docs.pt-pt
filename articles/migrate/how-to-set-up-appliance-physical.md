---
title: Configurar um dispositivo de migrações para Azure para servidores físicos
description: Saiba como configurar um dispositivo de migrações para Azure para avaliação de servidor físico.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: db67defc72dcc7d913f897c6fb61548c5c33cf52
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278321"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um dispositivo para servidores físicos

Este artigo descreve como configurar o dispositivo de migrações para Azure se você estiver avaliando servidores físicos com a ferramenta migrações para Azure: Server Assessment.

> [!NOTE]
> Se os recursos forem mencionados aqui que você ainda não vê no portal de migrações para Azure, aguarde. Eles serão exibidos na próxima semana ou assim.

O dispositivo de migrações para Azure é um dispositivo leve, usado pela avaliação de servidor de migrações para Azure para fazer o seguinte:

- Descobrir servidores locais.
- Enviar dados de desempenho e metadados para servidores descobertos para avaliação do Azure migrar servidor.

[Saiba mais](migrate-appliance.md) sobre o dispositivo migrações para Azure.


## <a name="appliance-deployment-steps"></a>Etapas de implantação do dispositivo

Para configurar o dispositivo, você:
- Baixe um arquivo compactado com o script do instalador de migrações para Azure do portal do Azure.
- Extraia o conteúdo do arquivo compactado. Inicie o console do PowerShell com privilégios administrativos.
- Execute o script do PowerShell para iniciar o aplicativo Web do dispositivo.
- Configure o dispositivo pela primeira vez e registre-o com o projeto de migrações para Azure.

## <a name="download-the-installer-script"></a>Baixar o script do instalador

Baixe o arquivo compactado para o dispositivo.

1. Em **metas de migração** > **servidores** > **migrações para Azure: avaliação do servidor**, clique em **descobrir**.
2. No **Discover machines** > **seus computadores são virtualizados?** , clique em **não virtualizado/outro**.
3. Clique em **baixar** para baixar o arquivo compactado.

    ![Baixar VM](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o arquivo compactado é seguro, antes de implantá-lo.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Execute o comando a seguir para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão mais recente do dispositivo, o hash gerado deve corresponder a essas configurações.

  **Algoritmo** | **Valor de hash**
  --- | ---
  MD5 | 96fd99581072c400aa605ab036a0a7c0
  SHA256 | f5454beef510c0aa38ac1c6be6346207c351d5361afa0c9cea4772d566fcdc36



## <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalador de migrações para Azure
= O script do instalador faz o seguinte:

- Instala agentes e um aplicativo Web para avaliação e descoberta de servidor físico.
- Instale as funções do Windows, incluindo o serviço de ativação do Windows, o IIS e o ISE do PowerShell.
- Baixe e instale um módulo regravável do IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave do registro (HKLM) com detalhes de configuração persistente para migrações para Azure.
- Cria os seguintes arquivos no caminho:
    - **Arquivos de configuração**:%ProgramData%\Microsoft Azure\Config
    - **Arquivos de log**:%ProgramData%\Microsoft Azure\Logs

Execute o script da seguinte maneira:

1. Extraia o arquivo compactado em uma pasta no servidor que hospedará o dispositivo.
2. Inicie o PowerShell no servidor acima com privilégio administrativo (elevado).
3. Altere o diretório do PowerShell para a pasta em que o conteúdo foi extraído do arquivo compactado baixado.
4. Execute o script executando o seguinte comando:
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
O script iniciará o aplicativo Web do dispositivo quando ele for concluído com êxito.



### <a name="verify-appliance-access-to-azure"></a>Verificar o acesso do dispositivo ao Azure

Verifique se a VM do dispositivo pode se conectar às [URLs do Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)necessárias.

## <a name="configure-the-appliance"></a>Configurar o dispositivo

Configure o dispositivo pela primeira vez.

1. Abra um navegador em qualquer computador que possa se conectar à VM e abra a URL do aplicativo Web do dispositivo: **https://*nome do dispositivo ou endereço IP*: 44368**.

   Como alternativa, você pode abrir o aplicativo na área de trabalho clicando no atalho do aplicativo.
2. No aplicativo Web > **configurar os pré-requisitos**, faça o seguinte:
    - **Licença**: aceite os termos de licença e leia as informações de terceiros.
    - **Conectividade**: o aplicativo verifica se a VM tem acesso à Internet. Se a VM usar um proxy:
        - Clique em **configurações de proxy**e especifique o endereço de proxy e a porta de escuta, no formato http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de horário**: o tempo é verificado. O tempo no dispositivo deve ser sincronizado com o horário da Internet para que a descoberta da VM funcione corretamente.
    - **Instalar atualizações**: avaliação do servidor de migrações para Azure verifica se o dispositivo tem as atualizações mais recentes instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrar o dispositivo com as migrações para Azure

1. Clique em **fazer logon**. Se não aparecer, verifique se você desabilitou o bloqueador de pop-ups no navegador.
2. Na nova guia, entre usando suas credenciais do Azure.
    - Entre com seu nome de usuário e senha.
    - Não há suporte para a entrada com um PIN.
3. Depois de entrar com êxito, volte para o aplicativo Web.
4. Selecione a assinatura na qual o projeto de migração do Azure foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o dispositivo. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **registrar**.


## <a name="start-continuous-discovery"></a>Iniciar descoberta contínua

Conecte-se do dispositivo a servidores físicos e inicie a descoberta.

1. Clique em **Adicionar credenciais** para especificar as credenciais de conta que o dispositivo usará para descobrir servidores.  
2. Especifique o **sistema operacional**, o nome amigável para as credenciais, o **nome de usuário** e a **senha** e clique em **Adicionar**.
Você pode adicionar um conjunto de credenciais para servidores Windows e Linux.
4. Clique em **Adicionar servidor**e especifique os detalhes do servidor – endereço FQDN/IP e nome amigável das credenciais (uma entrada por linha) para se conectar ao servidor.
3. Clique em **validar**. Após a validação, a lista de servidores que podem ser descobertos é mostrada.
    - Se a validação falhar para um servidor, examine o erro passando o mouse sobre o ícone na coluna **status** . Corrija os problemas e valide novamente.
    - Para remover um servidor, selecione > **excluir**.
4. Após a validação, clique em **salvar e inicie a descoberta** para iniciar o processo de descoberta.

Isso inicia a descoberta. Leva cerca de 15 minutos para que os metadados de VMs descobertas apareçam na portal do Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após a conclusão da descoberta, você pode verificar se os servidores aparecem no Portal.

1. Abra o painel migrações para Azure.
2. Na página **migrações para Azure – servidores** > **migrações para Azure: avaliação do servidor** , clique no ícone que exibe a contagem de **servidores descobertos**.


## <a name="next-steps"></a>Passos Seguintes

Experimente a [avaliação de servidores físicos](tutorial-assess-physical.md) com a avaliação de servidor de migrações para Azure.
