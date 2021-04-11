---
title: Crie um aparelho Azure Migrate para servidores físicos
description: Saiba como configurar um aparelho Azure Migrate para a descoberta e avaliação física do servidor.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 9052cbd3dc728b50b62c33f3a11a5e36a7504f29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771571"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um aparelho para servidores físicos

Este artigo descreve como configurar o aparelho Azure Migrate se estiver a avaliar servidores físicos com a ferramenta Azure Migrate: Discovery and assessment.

O aparelho Azure Migrate é um aparelho leve, utilizado pela Azure Migrate: Descoberta e avaliação para fazer o seguinte:

- Descubra servidores no local.
- Envie metadados e dados de desempenho para servidores descobertos para Azure Migrate: Descoberta e avaliação.

[Saiba mais](migrate-appliance.md) sobre o aparelho Azure Migrate.


## <a name="appliance-deployment-steps"></a>Etapas de implantação de aparelhos

Para configurar o aparelho:

- Forneça um nome de aparelho e gere uma chave de projeto no portal.
- Descarregue um ficheiro com fecho de correr com o script do instalador Azure Migrate a partir do portal Azure.
- Extrair o conteúdo do ficheiro com fecho. Lançar a consola PowerShell com privilégios administrativos.
- Execute o script PowerShell para lançar a aplicação web do aparelho.
- Configure o aparelho pela primeira vez e registe-o com o projeto utilizando a chave do projeto.

### <a name="generate-the-project-key"></a>Gerar a chave do projeto

1. Em **Objetivos de Migração**  >  **Windows, Linux e SQL Servers**  >  **Azure Migrate: Discovery and assessment**, selecione **Discover**.
2. In **Discover servers**  >  **Are your servers Are your servers virtualized?** 
3. Em **1:Gere a tecla do projeto,** forneça um nome para o aparelho Azure Migrate que irá configurar para a descoberta de servidores físicos ou virtuais. O nome deve ser alfanumérico com 14 caracteres ou menos.
1. Clique na **chave Gerar** para iniciar a criação dos recursos Azure necessários. Não feche a página de servidores Discover durante a criação de recursos.
1. Após a criação bem sucedida dos recursos Azure, gera-se uma **chave de projeto.**
1. Copie a chave pois necessitará para completar o registo do aparelho durante a sua configuração.

### <a name="download-the-installer-script"></a>Descarregue o script do instalador

Em **2: Descarregue o aparelho Azure Migrate,** clique em **Baixar**.

   ![Seleções para máquinas Discover](./media/tutorial-assess-physical/servers-discover.png)


   ![Seleções para Gerar Tecla](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. Nos servidores para os quais descarregou o ficheiro, abra uma janela de comando do administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Uso de exemplo para nuvem governamental: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifique a versão mais recente do aparelho e as definições de [valores de haxixe.](tutorial-discover-physical.md#verify-security)
 

## <a name="run-the-azure-migrate-installer-script"></a>Executar o script do instalador Azure Migrate
O script do instalador faz o seguinte:

- Instala agentes e uma aplicação web para descoberta e avaliação física do servidor.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo reescrito IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistente para Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Config Ficheiros**: %Programdata%\Microsoft Azure\Config
    - **Ficheiros de registo**: %Programdata%\Microsoft Azure\Logs

Execute o guião da seguinte forma:

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho.  Certifique-se de que não executa o script num servidor com um aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde o conteúdo foi extraído do ficheiro fechado descarregado.
4. Executar o script nomeado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Para o Governo de Azure: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    O script lançará a aplicação web do aparelho quando terminar com sucesso.

Se encontrar algum problema, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timetamp</em>.log para resolução de problemas.



### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

### <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

1. Abra um browser em qualquer máquina que possa ligar ao aparelho e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço *IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho clicando no atalho da aplicação.
2. Aceite os termos da **licença** e leia as informações de terceiros.
1. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Conectividade**: A aplicação verifica se o servidor tem acesso à Internet. Se o servidor utilizar um representante:
        - Clique no **proxy configurar** e especifique o endereço de procuração (no formulário http://ProxyIPAddress ou na porta de http://ProxyFQDN) audição.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
        - Se tiver adicionado detalhes de procuração ou desativado o proxy e/ou autenticação, clique em **Guardar** para ativar novamente a verificação de conectividade.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta do servidor funcione corretamente.
    - **Instalar atualizações**: Azure Migrate: A descoberta e avaliação verificam se o aparelho tem as últimas atualizações instaladas. Depois de concluída a verificação, pode clicar nos **serviços do aparelho para** ver o estado e as versões dos componentes em funcionamento no aparelho.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Cole a chave do **projeto** copiada do portal. Se não tiver a chave, vá ao **Azure Migrate: Descoberta e avaliação> Descubra> Gerir os aparelhos existentes**, selecione o nome do aparelho que forneceu no momento da geração chave e copie a chave correspondente.
1. Necessitará de um código de dispositivo para autenticar com o Azure. Clicar no **Login** abrirá um código modal com o código do dispositivo, como mostrado abaixo.

    ![Modal mostrando o código do dispositivo](./media/tutorial-discover-vmware/device-code.png)

1. Clique no **código copy & Iniciar sessão** para copiar o código do dispositivo e abrir um pedido de Login Azure num novo separador de navegador. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
1. No novo separador, cole o código do dispositivo e inscreva-se utilizando o seu nome de utilizador Estaure e a palavra-passe.
   
   O s-in com um PIN não é suportado.
3. Caso feche o separador de login acidentalmente sem iniciar sessão, é necessário atualizar o separador de navegador do gestor de configuração do aparelho para ativar novamente o botão Iniciar sessão.
1. Depois de iniciar sessão com sucesso, volte ao separador anterior com o gestor de configuração do aparelho.
4. Se a conta de utilizador Azure utilizada para a exploração madeireira tiver as [permissões certas](./tutorial-discover-physical.md) sobre os recursos Azure criados durante a geração chave, o registo do aparelho será iniciado.
1. Depois de o aparelho estar registado com sucesso, pode ver os dados do registo clicando nos **detalhes do Ver.**


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Agora, ligue-se do aparelho aos servidores físicos a descobrir e inicie a descoberta.

1. No **Passo 1: Forneça credenciais para a descoberta de servidores físicos ou virtuais do Windows e Linux,** clique em **Adicionar credenciais**.
1. Para o servidor do Windows, selecione o tipo de fonte como **Windows Server**, especifique um nome amigável para credenciais, adicione o nome de utilizador e a palavra-passe. Clique em **Guardar**.
1. Se estiver a utilizar a autenticação baseada em palavras-passe para o servidor Linux, selecione o tipo de fonte como **Linux Server (baseado em palavras-passe)**, especifique um nome amigável para credenciais, adicione o nome de utilizador e a palavra-passe. Clique em **Guardar**.
1. Se estiver a utilizar a autenticação baseada em chaves SSH para o servidor Linux, pode selecionar o tipo de fonte como **Linux Server (baseado em teclas SSH)**, especifique um nome amigável para credenciais, adicione o nome de utilizador, navegue e selecione o ficheiro de chave privada SSH. Clique em **Guardar**.

    - Azure Migrate suporta a chave privada SSH gerada pelo comando ssh-keygen usando algoritmos RSA, DSA, ECDSA e ed25519.
    - Atualmente, a Azure Migrate não suporta a chave SSH baseada em palavras-passe. Utilize uma chave SSH sem uma palavra-passe.
    - Atualmente, a Azure Migrate não suporta ficheiro chave ssh gerado pela PuTTY.
    - A Azure Migrate suporta o formato OpenSSH do ficheiro de chave privada SSH, conforme mostrado abaixo:
    
    ![Formato suportado por chave privada SSH](./media/tutorial-discover-physical/key-format.png)
1. Se quiser adicionar várias credenciais ao mesmo tempo, clique em **Adicionar mais** para guardar e adicionar mais credenciais. Várias credenciais são suportadas para a descoberta de servidores físicos.
1. No **Passo 2:Forneça detalhes físicos ou virtuais do servidor**, clique na fonte de descoberta **Adicionar** para especificar o **endereço IP/FQDN** do servidor e o nome amigável para credenciais para se ligar ao servidor.
1. Pode **adicionar um único item** de cada vez ou adicionar **vários itens** de uma só vez. Existe também uma opção para fornecer detalhes do servidor através **do Import CSV**.

    ![Seleções para adicionar fonte de descoberta](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - Se escolher **Adicionar um único item,** pode escolher o tipo de SO, especificar o nome amigável para credenciais, adicionar endereço **IP/FQDN** do servidor e clicar em **Guardar**.
    - Se escolher **Adicionar vários itens,** pode adicionar vários registos ao mesmo tempo especificando **o endereço IP/FQDN** do servidor com o nome amigável para credenciais na caixa de texto. Verifique** os registos adicionados e clique em **Guardar**.
    - Se escolher **Import CSV** _(selecionado por padrão),_ pode descarregar um ficheiro de modelo CSV, preencher o ficheiro com o endereço **IP/FQDN** do servidor e nomear um nome amigável para credenciais. Em seguida, importa o ficheiro para o aparelho, **verifique** os registos do ficheiro e clique em **Guardar**.

1. Ao clicar em Guardar, o aparelho tentará validar a ligação aos servidores adicionados e mostrar o **estado de Validação** na tabela contra cada servidor.
    - Se a validação falhar para um servidor, reveja o erro clicando na **Validação falhada** na coluna 'Estado' da tabela. Corrija o problema e valide novamente.
    - Para remover um servidor, clique em **Apagar**.
1. Pode **revalidar** a conectividade aos servidores a qualquer momento antes de iniciar a descoberta.
1. Clique em **Iniciar a descoberta**, para iniciar a descoberta dos servidores validados com sucesso. Após a descoberta ter sido iniciada com sucesso, pode verificar o estado de descoberta contra cada servidor da tabela.


Isto começa a ser descoberto. Leva aproximadamente 2 minutos por servidor para que os metadados do servidor descoberto apareçam no portal Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após o fim da descoberta, pode verificar se os servidores aparecem no portal.

1. Abra o dashboard do Azure Migrate.
2. Em **Azure Migrate - Windows, Linux e SQL Servers**  >  **Azure Migrate: Página de descoberta e avaliação,** clique no ícone que exibe a contagem para **servidores Descobertos**.


## <a name="next-steps"></a>Passos seguintes

Experimente [a avaliação dos servidores físicos](tutorial-assess-physical.md) com a Azure Migrate: Descoberta e avaliação.