---
title: Crie um aparelho Azure Migrate para servidores físicos
description: Saiba como configurar um aparelho Azure Migrate para avaliação física do servidor.
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: 6d9cc071ad5d81a09a14b12fe2acdf564c2ea6c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331785"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Configurar um aparelho para servidores físicos

Este artigo descreve como configurar o aparelho Azure Migrate se estiver a avaliar servidores físicos com a ferramenta Azure Migrate: Server Assessment.

O aparelho Azure Migrate é um aparelho leve, utilizado pela Azure Migrate Server Assessment para fazer o seguinte:

- Descubra servidores no local.
- Envie metadados e dados de desempenho para servidores descobertos para a Avaliação do Servidor migratório Azure.

[Saiba mais](migrate-appliance.md) sobre o aparelho Azure Migrate.


## <a name="appliance-deployment-steps"></a>Etapas de implantação de aparelhos

Para configurar o aparelho:
- Descarregue um ficheiro com fecho de correr com o script do instalador Azure Migrate a partir do portal Azure.
- Extrair o conteúdo do ficheiro com fecho. Lançar a consola PowerShell com privilégios administrativos.
- Execute o script PowerShell para lançar a aplicação web do aparelho.
- Configure o aparelho pela primeira vez e registe-o com o projeto Azure Migrate.

## <a name="download-the-installer-script"></a>Descarregue o script do instalador

Descarregue o ficheiro com fecho para o aparelho.

1. In **Migration Goals**  >  **Servers**  >  **Azure Migrate: Server Assessment**, click **.click**.
2. In **Discover machines**  >  **Are your machines virtualized?** **Not virtualized/Other**
3. Clique **em Baixar** para descarregar o ficheiro com fecho de correr.

    ![Baixar VM](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Verificar segurança

Verifique se o ficheiro com fecho está seguro, antes de o colocar.

1. No computador para o qual transferiu o ficheiro, abra uma janela de comando de administrador.
2. Executar o seguinte comando para gerar o haxixe para o ficheiro zipped:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Uso de exemplo para nuvem pública:```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Uso de exemplo para nuvem governamental:```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Verifique a versão mais recente do aparelho e os valores do haxixe:
 
    - Para a nuvem pública:

        **Cenário** | **Baixar*** | **Valor de hash**
        --- | --- | ---
        Físico (63,1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac088850567cb57c9906fbc3b85a0

    - Para o Governo de Azure:

        **Cenário** | **Baixar*** | **Valor de hash**
        --- | --- | ---
        Físico (63,1 MB) | [Versão mais recente](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cf3f9e1e61c9b37e1


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

1. Extraia o ficheiro com fecho para uma pasta no servidor que irá hospedar o aparelho.  Certifique-se de que não coloca o guião numa máquina num aparelho Azure Migrate existente.
2. Lançar PowerShell no servidor acima com privilégio administrativo (elevado).
3. Mude o diretório PowerShell para a pasta onde o conteúdo foi extraído do ficheiro fechado descarregado.
4. Executar o script nomeado **AzureMigrateInstaller.ps1** executando o seguinte comando:

    - Para a nuvem pública:``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Para o Governo de Azure:``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    O script lançará a aplicação web do aparelho quando terminar com sucesso.

Se encontrar algum problema, pode aceder aos registos de scripts em C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log para resolução de problemas.



### <a name="verify-appliance-access-to-azure"></a>Verifique o acesso do aparelho ao Azure

Certifique-se de que o aparelho VM pode ligar-se aos URLs Azure para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)

## <a name="configure-the-appliance"></a>Configure o aparelho

Coloque o aparelho pela primeira vez.

1. Abra um browser em qualquer máquina que possa ligar ao VM e abra o URL da aplicação web do aparelho: **https:// nome do aparelho ou endereço*IP*: 44368**.

   Em alternativa, pode abrir a aplicação a partir do ambiente de trabalho clicando no atalho da aplicação.
2. Na aplicação web > **Configurar pré-requisitos,** faça o seguinte:
    - **Licença**: Aceite os termos da licença e leia as informações de terceiros.
    - **Conectividade**: A aplicação verifica se o VM tem acesso à Internet. Se o VM utilizar um representante:
        - Clique **nas definições de Procuração**e especifique o endereço de procuração e a porta de audição, no formulário http://ProxyIPAddress ou http://ProxyFQDN .
        - Especifique as credenciais se o proxy precisar de autenticação.
        - Apenas é suportado o proxy HTTP.
    - **Sincronização temporal:** O tempo é verificado. O tempo do aparelho deve estar sincronizado com o tempo de internet para que a descoberta de VM funcione corretamente.
    - **Instalar atualizações**: A Azure Migrate Server Assessment verifica se o aparelho tem as últimas atualizações instaladas.

### <a name="register-the-appliance-with-azure-migrate"></a>Registe o aparelho com a Azure Migrate

1. Clique **em iniciar sessão**. Se não aparecer, certifique-se de ter desativado o bloqueador pop-up no navegador.
2. No novo separador, inscreva-se utilizando as suas credenciais Azure.
    - Inscreva-se com o seu nome de utilizador e senha.
    - O s-in com um PIN não é suportado.
3. Depois de iniciar sessão com sucesso, volte à aplicação web.
4. Selecione a subscrição em que o projeto Azure Migrate foi criado. Em seguida, selecione o projeto.
5. Especifique um nome para o aparelho. O nome deve ser alfanumérico com 14 caracteres ou menos.
6. Clique em **Registar**.


## <a name="start-continuous-discovery"></a>Iniciar a descoberta contínua

Ligue do aparelho aos servidores físicos e inicie a descoberta.

1. Clique **em Adicionar Credenciais** para especificar as credenciais de conta que o aparelho utilizará para descobrir servidores.  
2. Especifique o **Sistema Operativo,** um nome amigável para as credenciais, e o nome de utilizador e senha. Em seguida, clique em **Adicionar**.
Pode adicionar um conjunto de credenciais cada um para servidores Windows e Linux.
4. Clique **em Adicionar servidor**e especifique detalhes do servidor- Endereço FQDN/IP e nome amigável de credenciais (uma entrada por linha) para ligar ao servidor.
3. Clique em **Validar**. Após a validação, é mostrada a lista de servidores que podem ser descobertos.
    - Se a validação falhar para um servidor, reveja o erro pairando sobre o ícone na coluna **Status.** Corrija problemas e valide novamente.
    - Para remover um servidor, selecione > **Eliminar**.
4. Após validação, clique em **Guardar e iniciar** a descoberta para iniciar o processo de descoberta.

Isto começa a ser descoberto. Leva cerca de 15 minutos para que os metadados de VMs descobertos apareçam no portal Azure.

## <a name="verify-servers-in-the-portal"></a>Verificar servidores no portal

Após o fim da descoberta, pode verificar se os servidores aparecem no portal.

1. Abra o painel Azure Migrate.
2. Em **Azure Migrate - Servidores**  >  **Azure Migrate:** Página de Avaliação do servidor, clique no ícone que exibe a contagem para **servidores descobertos**.


## <a name="next-steps"></a>Próximos passos

Experimente [a avaliação dos servidores físicos](tutorial-assess-physical.md) com a Avaliação do Servidor Azure Migrate.
