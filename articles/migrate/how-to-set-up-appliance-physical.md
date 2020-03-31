---
title: Instale um aparelho Azure Migrate para servidores físicos
description: Aprenda a configurar um aparelho Azure Migrate para avaliação física do servidor.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: b60a30e5e30ee81cbaca7d5e4691ccedac2462b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598175"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um aparelho para servidores físicos

Este artigo descreve como configurar o aparelho Migratório Azure se estiver a avaliar servidores físicos com a ferramenta Azure Migrate: Server Assessment.

O aparelho Azure Migrate é um aparelho leve, utilizado pela Azure Migrate Server Assessment para fazer o seguinte:

- Descubra servidores no local.
- Envie metadados e dados de desempenho para servidores descobertos para a Avaliação do Servidor Migratório Azure.

[Saiba mais](migrate-appliance.md) sobre o aparelho Azure Migrate.


## <a name="appliance-deployment-steps"></a>Passos de implantação de aparelhos

Para configurar o aparelho:
- Descarregue um ficheiro com fecho com o script do instalador Azure Migrate a partir do portal Azure.
- Extrair o conteúdo do ficheiro com fecho. Lance a consola PowerShell com privilégios administrativos.
- Execute o script PowerShell para lançar a aplicação web do aparelho.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-installer-script"></a>Descarregue o script do instalador

Descarregue o ficheiro zipped para o aparelho.

1. Em **Objetivos** > de Migração**Servidores** > **Azure Migrar: Avaliação do servidor,** clique **em Descobrir**.
2. Em **Discover machines** > **As suas máquinas estão virtualizadas?** **Not virtualized/Other**
3. Clique em **Baixar** para descarregar o ficheiro zipped.

    ![Baixar VM](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verificar a segurança

Verifique se o ficheiro com fecho está seguro, antes de o implantar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o hash para o VHD
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Utilização de exemplo: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  Para a versão mais recente do aparelho, o hash gerado deve coincidir com estas [definições](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical#verify-security).



## <a name="run-the-azure-migrate-installer-script"></a>Executar o script de instalação Azure Migrate
O script do instalador faz o seguinte:

- Instala agentes e uma aplicação web para descoberta e avaliação de servidores físicos.
- Instale funções windows, incluindo o Windows Activation Service, IIS e PowerShell ISE.
- Descarregue e instale um módulo rewritável IIS. [Saiba mais](https://www.microsoft.com/download/details.aspx?id=7435).
- Atualiza uma chave de registo (HKLM) com detalhes de definição persistentes para o Azure Migrate.
- Cria os seguintes ficheiros no caminho:
    - **Ficheiros Config**: %Programdata%\Microsoft Azure\Config
    - **Ficheiros de Registo**: %Programdata%\Microsoft Azure\Logs

Executar o guião da seguinte forma:

1. Extraio o ficheiro com fecho numa pasta no servidor que irá alojar o aparelho.
2. Lance powerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde os conteúdos foram extraídos do ficheiro com fecho descarregado.
4. Executar o script chamado **AzureMigrateInstaller.ps1** executando o seguinte comando:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
O script lançará a aplicação web do aparelho quando terminar com sucesso.

Em caso de problemas, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log for troubleshooting.

> [!NOTE]
> Por favor, não execute o script do instalador Azure Migrate num aparelho de migração Azure existente.

### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o VM do aparelho pode ligar-se aos [URLs Azure necessários](migrate-appliance.md#url-access).

## <a name="configure-the-appliance"></a>Configure o aparelho

Instale o aparelho pela primeira vez.

1. Abra um navegador em qualquer máquina que possa ligar-se ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho clicando no atalho da aplicação.
2. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um proxy:
        - Clique em **definições proxy**, e especifique o endereço proxy e a porta de escuta, no formulário http://ProxyIPAddress ou http://ProxyFQDN.
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização de tempo**: O tempo é verificado. O tempo no aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
    - **Instalar atualizações**: A Avaliação do Servidor Migratório Azure verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de que desativou o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - O sessão com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte para a aplicação web.
4. Selecione a subscrição em que foi criado o projeto Azure Migrate. Em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique no **Registo**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue-se do aparelho aos servidores físicos e inicie a descoberta.

1. Clique em **Adicionar Credenciais** para especificar as credenciais de conta que o aparelho utilizará para descobrir servidores.  
2. Especifique o **Sistema Operativo**, nome amigável para as credenciais, **nome de utilizador** e **palavra-passe** e clique em **Adicionar**.
Pode adicionar um conjunto de credenciais cada um para servidores Windows e Linux.
4. Clique em **Adicionar servidor**, e especifique os detalhes do servidor - endereço FQDN/IP e nome amigável de credenciais (uma entrada por linha) para ligar ao servidor.
3. Clique em **Validar**. Após validação, a lista de servidores que podem ser descobertos é mostrada.
    - Se a validação falhar num servidor, reveja o erro pairando sobre o ícone na coluna **'Estado'.** Corrija os problemas e valide novamente.
    - Para remover um servidor, selecione > **Eliminar**.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal Azure.

## <a name="verify-servers-in-the-portal"></a>Verifique os servidores no portal

Após a descoberta terminar, pode verificar se os servidores aparecem no portal.

1. Abra o painel de migração Azure.
2. No **Azure Migrate - Servers** > **Azure Migrar:** Página de Avaliação do Servidor, clique no ícone que apresenta a contagem para **servidores descobertos**.


## <a name="next-steps"></a>Passos seguintes

Experimente [a avaliação dos servidores físicos](tutorial-assess-physical.md) com a Avaliação do Servidor Migratório Azure.
