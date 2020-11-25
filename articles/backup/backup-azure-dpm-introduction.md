---
title: Prepare o servidor DPM para fazer o back-ups
description: Neste artigo, aprenda a preparar-se para backups do System Center Data Protection Manager (DPM) para a Azure, utilizando o serviço de Backup Azure.
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 0089c3d86eb36b82287570ecdfd6e8c782e6fb8a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002869"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Prepare-se para apoiar cargas de trabalho para Azure com O Centro de SistemaS DPM

Este artigo explica como se preparar para backups do System Center Data Protection Manager (DPM) para a Azure, utilizando o serviço de Backup Azure.

O artigo fornece:

- Uma visão geral da implementação do DPM com a Azure Backup.
- Pré-requisitos e limitações para a utilização de Backup Azure com DPM.
- Passos para preparar o Azure, incluindo a criação de um cofre de backup dos Serviços de Recuperação, e modificação opcional do tipo de armazenamento Azure para o cofre.
- Passos para preparar o servidor DPM, incluindo o descarregamento de credenciais de cofre, a instalação do agente Azure Backup e o registo do servidor DPM no cofre.
- Dicas de resolução de problemas para erros comuns.

## <a name="why-back-up-dpm-to-azure"></a>Por que apoiar o DPM para Azure?

[O Sistema Center DPM](/system-center/dpm/dpm-overview) confirma os dados de ficheiros e aplicações. O DPM interage com a Azure Backup da seguinte forma:

- **DPM em execução em um servidor físico ou no local VM** - Pode fazer cópias de segurança para um cofre de backup em Azure, além de backup de disco e fita adesiva.
- **DPM em execução num Azure VM** - Do System Center 2012 R2 com atualização 3 ou mais tarde, pode implementar DPM num VM Azure. Pode fazer cópias de segurança de dados para discos Azure ligados ao VM ou utilizar a Cópia de Segurança Azure para fazer o backup dos dados para um cofre de reserva.

Os benefícios empresariais de apoiar os servidores DPM para a Azure incluem:

- Para o DPM no local, o Azure Backup fornece uma alternativa à implementação a longo prazo para fita adesiva.
- Para o DPM em execução num Azure VM, o Azure Backup permite-lhe descarregar o armazenamento do disco Azure. Armazenar dados mais antigos num cofre de backup permite-lhe aumentar o seu negócio armazenando novos dados para o disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Definição** | **Requisito**
--- | ---
DPM em um Azure VM | System Center 2012 R2 com Rollup de atualização R2 3 ou mais tarde do DPM 2012 R2.
DPM em um servidor físico | System Center 2012 SP1 ou posterior; Centro de Sistema 2012 R2.
DPM em um VM Hiper-V | System Center 2012 SP1 ou posterior; Centro de Sistema 2012 R2.
DPM em um VMware VM | System Center 2012 R2 com Update Rollup 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e o .NET Framework 4.5 instalados.
Aplicações suportadas | [Saiba](/system-center/dpm/dpm-protection-matrix) o que o DPM pode recuar.
Tipos de ficheiros suportados | Estes tipos de ficheiros podem ser apoiados com cópia de segurança do Azure:<br> <li>Encriptado (apenas cópias de segurança completas)<li> Comprimido (cópias de segurança incrementais suportadas) <li> Escassos (backups incrementais suportados)<li> Comprimido e escasso (tratado como escasso)
Tipos de ficheiros não suportados | <li>Servidores em sistemas de ficheiros sensíveis a casos<li> links rígidos (ignorados)<li> reparse pontos (ignorado)<li> encriptado e comprimido (ignorado)<li> encriptado e escasso (ignorado)<li> Fluxo comprimido<li> fluxo de parse
Armazenamento local | Cada máquina que pretende fazer back-up deve ter armazenamento gratuito local que é pelo menos 5% do tamanho dos dados que estão sendo apoiados. Por exemplo, o backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre na localização do risco.
Armazenamento de cofre | Não há limite para a quantidade de dados que pode fazer até um cofre de backup Azure, mas o tamanho de uma fonte de dados (por exemplo, uma máquina virtual ou base de dados) não deve exceder 54.400 GB.
Azure ExpressRoute | Pode fazer o back up dos seus dados através do Azure ExpressRoute com o espreitamento público (disponível para circuitos antigos) e o espreitamento da Microsoft. Backup sobre olhando privado não é apoiado.<br/><br/> **Com o olhar público**: Garantir o acesso aos seguintes domínios/endereços:<br/><br/>- `http://www.msftncsi.com/ncsi.txt` <br/><br/>- `microsoft.com` <br/><br/>-`.WindowsAzure.com`<br/><br/>-`.microsoftonline.com`<br/><br/>-`.windows.net`<br/><br/> **Com o espreguio da Microsoft,** selecione os seguintes serviços/regiões e valores comunitários relevantes:<br/><br/>- Diretório Ativo Azure (12076:5060)<br/><br/>- Região microsoft Azure (de acordo com a localização do cofre dos Serviços de Recuperação)<br/><br/>- Azure Storage (de acordo com a localização do cofre dos Serviços de Recuperação)<br/><br/>Para obter mais informações, consulte [os requisitos de encaminhamento ExpressRoute](../expressroute/expressroute-routing.md).<br/><br/>**Nota:** O olhar público é precotado para novos circuitos.
Agente do Backup do Azure | Se o DPM estiver em funcionamento no System Center 2012 SP1, instale o Rollup 2 ou mais tarde para o DPM SP1. Isto é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implementar a versão mais recente do agente Azure Backup, também conhecido como o agente do Microsoft Azure Recovery Service (MARS). Se tiver uma versão anterior implementada, atualize a versão mais recente para garantir que a cópia de segurança funciona como esperado.

Antes de começar, precisa de uma conta Azure com a funcionalidade Azure Backup ativada. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Leia mais sobre os [preços do Microsoft Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar as definições de armazenamento

Pode escolher entre armazenamento geo-redundante e armazenamento localmente redundante.

- Por predefinição, o seu cofre tem um armazenamento georredundante.
- Se o cofre for o seu apoio primário, deixe a opção definida para armazenamento geo-redundante. Se quiser uma opção mais barata que não seja tão durável, use o seguinte procedimento para configurar o armazenamento localmente redundante.
- Saiba mais sobre o [armazenamento do Azure](../storage/common/storage-redundancy.md), e as opções de armazenamento [geo-redundantes,](../storage/common/storage-redundancy.md#geo-redundant-storage) [localmente redundantes](../storage/common/storage-redundancy.md#locally-redundant-storage) e [redundantes.](../storage/common/storage-redundancy.md#zone-redundant-storage)
- Modifique as definições de armazenamento antes da cópia de segurança inicial. Se já tiver feito o backup de um item, pare de o apoiar no cofre antes de modificar as definições de armazenamento.

Para editar a definição de replicação de armazenamento:

1. Abre o painel do cofre.

2. In **Manage**, selecione **Backup Infrastructure**.

3. No menu **de Configuração de Cópia de Segurança,** selecione uma opção de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Transferir as credenciais do cofre

Usa credenciais de cofre quando regista o servidor DPM no cofre.

- O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre de cópia de segurança.
- O portal, em seguida, carrega a chave pública para o Access Control Service (ACS).
- Durante o fluxo de trabalho de registo da máquina, a chave privada do certificado é disponibilizada ao utilizador, que autentica a máquina.
- Com base na autenticação, o serviço de backup Azure envia dados para o cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Melhores práticas para credenciais de cofre

Para obter as credenciais, descarregue o ficheiro de credencial de cofre através de um canal seguro a partir do portal Azure:

- As credenciais do cofre são utilizadas apenas durante o fluxo de trabalho de registo.
- É sua responsabilidade garantir que o ficheiro de credenciais do cofre é seguro, e não comprometido.
  - Se o controlo das credenciais for perdido, as credenciais do cofre podem ser usadas para registar outras máquinas no cofre.
  - No entanto, os dados de backup são encriptados usando uma palavra-passe que lhe pertence, pelo que os dados de backup existentes não podem ser comprometidos.
- Certifique-se de que esse ficheiro é guardado num local que pode ser acedido a partir do servidor DPM. Se estiver armazenado numa partilha de ficheiros/SMB, verifique as permissões de acesso.
- As credenciais do cofre expiram após 48 horas. Você pode baixar novas credenciais de cofre quantas vezes for necessário. No entanto, apenas o último ficheiro de credencial de cofre pode ser utilizado durante o fluxo de trabalho de registo.
- O serviço de Backup Azure não tem conhecimento da chave privada do certificado, e a chave privada não está disponível no portal ou no serviço.

Descarregue o ficheiro de credenciais do cofre para uma máquina local da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o cofre no qual pretende registar o servidor DPM.
3. Em **Definições,** selecione **Propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Em **Properties**  >  **Credenciais de backup de propriedades**, selecione **Download**. O portal gera o ficheiro credencial do cofre usando uma combinação do nome do cofre e da data atual, e disponibiliza-o para download.

    ![Baixar credenciais](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. **Selecione Guardar** para descarregar as credenciais do cofre para a pasta ou **guardar como** e especificar uma localização. Levará até um minuto para o ficheiro ser gerado.

## <a name="install-the-backup-agent"></a>Instale o agente de backup

Todas as máquinas que são apoiadas pela Azure Backup devem ter o agente De backup (também conhecido como agente do Microsoft Azure Recovery Service (MARS) instalado nele. Instale o agente no servidor DPM da seguinte forma:

1. Abra o cofre ao qual pretende registar o servidor DPM.
2. Em **Definições,** selecione **Propriedades**.

    ![Abrir as definições do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página **Propriedades,** descarregue o Agente de Backup Azure.

    ![Download](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Depois de descarregar, faça MARSAgentInstaller.exe. para instalar o agente na máquina DPM.
5. Selecione uma pasta de instalação e uma pasta de cache para o agente. O espaço livre de localização da cache deve ser pelo menos 5% dos dados de backup.
6. Se utilizar um servidor proxy para se ligar à internet, no ecrã **de configuração Proxy,** insira os detalhes do servidor proxy. Se utilizar um representante autenticado, insira o nome de utilizador e os dados da palavra-passe neste ecrã.
7. O agente Azure Backup instala .NET Framework 4.5 e Windows PowerShell (se não estiverem instalados) para concluir a instalação.
8. Depois de instalar o agente, **feche** a janela.

    ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registar o servidor DPM no cofre

1. Na consola DPM Administrator > **Management**, selecione **Online**. Selecione **Registar**. Abrirá o Assistente do Servidor de Registo.
2. Na **Configuração Proxy**, especifique as definições de procuração conforme necessário.

    ![Configuração do proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. No **Cofre de Reserva,** navegue e selecione o ficheiro de credenciais do cofre que descarregou.

    ![Credenciais de cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. Na **Definição de Aceleração,** pode ativar opcionalmente o estrangulamento da largura de banda para cópias de segurança. Pode definir os limites de velocidade para especificar horas de trabalho e dias.

    ![Definição de estrangulamento](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Na **Definição da Pasta de Recuperação,** especifique uma localização que possa ser utilizada durante a recuperação de dados.

    - A Azure Backup utiliza esta localização como uma área de detenção temporária para dados recuperados.
    - Após terminar a recuperação de dados, o Azure Backup irá limpar os dados nesta área.
    - A localização deve ter espaço suficiente para guardar itens que espera recuperar em paralelo.

    ![Definição de pasta de recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Na **definição de encriptação,** gere ou forneça uma palavra-passe.

    - A palavra-passe é usada para encriptar as cópias de segurança para a nuvem.
    - Especifique um mínimo de 16 caracteres.
    - Guarde o ficheiro num local seguro, é necessário para a recuperação.

    ![Encriptação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > És dono da palavra-passe de encriptação e a Microsoft não tem visibilidade.
    > Se a palavra-passe for perdida ou esquecida, a Microsoft não pode ajudar a recuperar os dados de backup.

7. **Selecione Registar-se** para registar o servidor DPM no cofre.

Depois de o servidor ser registado com sucesso no cofre, está agora pronto para começar a fazer backup no Microsoft Azure. Terá de configurar o grupo de proteção na consola DPM para fazer o backs de carga de trabalho para o Azure. [Saiba como](/system-center/dpm/create-dpm-protection-groups) implantar grupos de proteção.

## <a name="troubleshoot-vault-credentials"></a>Credenciais de cofre de resolução de problemas

### <a name="expiration-error"></a>Erro de expiração

O ficheiro de credenciais do cofre é válido apenas por 48 horas (depois de ser descarregado do portal). Se encontrar algum erro neste ecrã (por exemplo, "Ficheiro de credenciais de cofre fornecido expirou"), inscreva-se no portal Azure e descarregue novamente o ficheiro de credenciais de cofre.

### <a name="access-error"></a>Erro de acesso

Certifique-se de que o ficheiro de credenciais do cofre está disponível num local que pode ser acedido pela aplicação de configuração. Se encontrar erros relacionados com o acesso, copie o ficheiro de credenciais do cofre para um local temporário nesta máquina e revendo a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválidos

Se encontrar um erro de credencial de cofre inválido (por exemplo, "Credenciais de abóbada inválidas fornecidas") o ficheiro é corrompido ou não tem as mais recentes credenciais associadas ao serviço de recuperação.

- Recave a operação depois de descarregar um novo ficheiro de credencial de cofre do portal.
- Este erro é normalmente visto quando seleciona a opção **de credencial de abóbada de descarregamento** no portal Azure, duas vezes em rápida sucessão. Neste caso, apenas o segundo ficheiro de credencial de cofre é válido.
