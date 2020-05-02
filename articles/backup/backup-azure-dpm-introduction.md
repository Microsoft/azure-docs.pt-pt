---
title: Prepare o servidor DPM para fazer o backcargas de trabalho
description: Neste artigo, aprenda a preparar-se para backups do System Center Data Protection Manager (DPM) para o Azure, utilizando o serviço de backup Azure.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273465"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Prepare-se para apoiar as cargas de trabalho para Azure com o Centro de Sistema DPM

Este artigo explica como se preparar para backups do System Center Data Protection Manager (DPM) para o Azure, utilizando o serviço de backup Azure.

O artigo fornece:

- Uma visão geral da implantação do DPM com o Azure Backup.
- Pré-requisitos e limitações para a utilização de Backup Azure com DPM.
- Passos para a preparação do Azure, incluindo a instalação de um cofre de backup de serviços de recuperação, e modificação opcional do tipo de armazenamento Azure para o cofre.
- Passos para preparar o servidor DPM, incluindo o descarregamento de credenciais de cofre, a instalação do agente De backup Azure e o registo do servidor DPM no cofre.
- Dicas de resolução de problemas para erros comuns.

## <a name="why-back-up-dpm-to-azure"></a>Por que apoiar o DPM para Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) backup ficheiros e dados de aplicação. DPM interage com Azure Backup da seguinte forma:

- **DPM em execução num servidor físico ou no local VM** — Pode fazer cópias de segurança para um cofre de backup em Azure, além de dispor de disco e cópia de segurança.
- **DPM em execução num Azure VM** — From System Center 2012 R2 com Update 3 ou posterior, pode implantar DPM num VM Azure. Pode fazer o backup de dados para discos Azure ligados ao VM ou utilizar o Azure Backup para fazer o backup dos dados para um cofre de backup.

Os benefícios empresariais de apoiar os servidores DPM para o Azure incluem:

- Para o DPM no local, o Azure Backup fornece uma alternativa à implementação a longo prazo para a fita.
- Para dPM em execução num Azure VM, o Azure Backup permite-lhe descarregar o armazenamento a partir do disco Azure. Armazenar dados mais antigos num cofre de backup permite-lhe aumentar o seu negócio armazenando novos dados em disco.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Definição** | **Requisito**
--- | ---
DPM em um VM Azure | System Center 2012 R2 com DPM 2012 R2 Update Rollup 3 ou posterior.
DPM em um servidor físico | System Center 2012 SP1 ou mais tarde; Centro de Sistema 2012 R2.
DPM em um VM Hiper-V | System Center 2012 SP1 ou mais tarde; Centro de Sistema 2012 R2.
DPM em um VMware VM | System Center 2012 R2 com Rollup update 5 ou posterior.
Componentes | O servidor DPM deve ter o Windows PowerShell e a .NET Framework 4.5 instalados.
Aplicações suportadas | [Saiba](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) o que o DPM pode fazer.
Tipos de ficheiro suportados | Estes tipos de ficheiros podem ser apoiados com cópia de segurança Azure: Encriptado (apenas cópias de segurança completas); Comprimido (backups incrementais suportados); Escassos (backups incrementais suportados); Comprimido e escasso (tratado como escasso).
Tipos de ficheiros não suportados | Servidores em sistemas de ficheiros sensíveis a casos; ligações duras (ignoradas); pontos de reparse (ignorado); encriptado e comprimido (ignorado); encriptado e escasso (ignorado); Fluxo comprimido; fluxo de parse.
Armazenamento local | Cada máquina que você quer fazer back deve ter armazenamento gratuito local que é pelo menos 5% do tamanho dos dados que estão sendo apoiados. Por exemplo, o backup de 100 GB de dados requer um mínimo de 5 GB de espaço livre no local do risco.
Armazenamento de cofre | Não há limite para a quantidade de dados que pode fazer até um cofre de backup Azure, mas o tamanho de uma fonte de dados (por exemplo, uma máquina virtual ou base de dados) não deve exceder 54.400 GB.
Azure ExpressRoute | Se o Azure ExpressRoute estiver configurado com o peering privado ou microsoft, não pode ser utilizado para fazer o backback dos dados para o Azure.<br/><br/> Se o Azure ExpressRoute estiver configurado com o Public Peering, pode ser usado para fazer o back-up dos dados ao Azure.<br/><br/> **Nota:** O Público está predestinado a novos circuitos.
Agente do Backup do Azure | Se o DPM estiver a funcionar no System Center 2012 SP1, instale o Rollup 2 ou mais tarde para dPM SP1. Isto é necessário para a instalação do agente.<br/><br/> Este artigo descreve como implementar a versão mais recente do agente De backup Azure, também conhecido como o agente do Microsoft Azure Recovery Service (MARS). Se tiver uma versão anterior implementada, atualize para a versão mais recente para garantir que a cópia de segurança funcione como esperado.

Antes de começar, precisa de uma conta Azure com a funcionalidade De backup Azure ativada. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Leia mais sobre os [preços do Microsoft Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Modificar as definições de armazenamento

Você pode escolher entre armazenamento geo-redundante e armazenamento localmente redundante.

- Por predefinição, o seu cofre tem um armazenamento georredundante.
- Se o cofre for o seu principal reforço, deixe a opção definida para armazenamento geo-redundante. Se quiser uma opção mais barata que não seja tão durável, utilize o seguinte procedimento para configurar o armazenamento localmente redundante.
- Saiba mais sobre o [armazenamento Azure,](../storage/common/storage-redundancy.md)e as opções de armazenamento [georedundante](../storage/common/storage-redundancy-grs.md) e [localmente redundantes.](../storage/common/storage-redundancy-lrs.md)
- Modificar as definições de armazenamento antes da cópia de segurança inicial. Se já tiver apoiado um item, pare de o apoiar no cofre antes de modificar as definições de armazenamento.

Para editar a definição de replicação de armazenamento:

1. Abra o painel do cofre.

2. Em **Gerir,** clique em Infraestrutura de **Backup**.

3. No menu **de configuração de backup,** selecione uma opção de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Transferir as credenciais do cofre

Usa credenciais de cofre quando regista o servidor DPM no cofre.

- O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre de cópia de segurança.
- O portal, em seguida, carrega a chave pública para o Access Control Service (ACS).
- Durante o fluxo de trabalho de registo da máquina, a chave privada do certificado é disponibilizada ao utilizador, que autentica a máquina.
- Com base na autenticação, o serviço de backup Azure envia dados para o cofre identificado.

### <a name="best-practices-for-vault-credentials"></a>Boas práticas para credenciais de cofre

Para obter as credenciais, descarregue o ficheiro credencial do cofre através de um canal seguro do portal Azure:

- As credenciais do cofre são usadas apenas durante o fluxo de trabalho de registo.
- É sua responsabilidade garantir que o ficheiro de credenciais do cofre é seguro e não comprometido.
  - Se o controlo das credenciais for perdido, as credenciais do cofre podem ser usadas para registar outras máquinas no cofre.
  - No entanto, os dados de backup são encriptados usando uma palavra-passe que pertence ao cliente, pelo que os dados de backup existentes não podem ser comprometidos.
- Certifique-se de que o ficheiro é guardado num local que pode ser acedido a partir do servidor DPM. Se estiver armazenado numa partilha de ficheiros/SMB, verifique se há permissões de acesso.
- As credenciais do cofre expiram após 48 horas. Pode baixar novas credenciais de cofre quantas vezes forem necessárias. No entanto, apenas o último ficheiro credencial do cofre pode ser utilizado durante o fluxo de trabalho de registo.
- O serviço de backup Azure não tem conhecimento da chave privada do certificado, e a chave privada não está disponível no portal ou no serviço.

Descarregue o ficheiro de credenciais do cofre para uma máquina local da seguinte forma:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Abra o cofre no qual pretende registar o servidor DPM.
3. Em **Definições,** clique em **Propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Nas**credenciais**de backup **de propriedades,** > clique em **Baixar**. O portal gera o ficheiro credencial do cofre usando uma combinação do nome do cofre e da data atual, e disponibiliza-o para download.

    ![Transferência](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Clique em **Guardar** para descarregar as credenciais do cofre para pasta, ou **Guardar As** e especificar uma localização. Levará até um minuto para que o ficheiro seja gerado.

## <a name="install-the-backup-agent"></a>Instalar o Agente de Backup

Todas as máquinas apoiadas pelo Azure Backup devem ter o agente de backup (também conhecido como o agente do Microsoft Azure Recovery Service (MARS) instalado no mesmo. Instale o agente no servidor DPM da seguinte forma:

1. Abra o cofre para o qual pretende registar o servidor DPM.
2. Em **Definições,** clique em **Propriedades**.

    ![Abrir o menu do cofre](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Na página **Propriedades,** descarregue o Agente de Backup Azure.

    ![Transferência](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. Depois de descarregar, execute MARSAgentInstaller.exe. para instalar o agente na máquina DPM.
5. Selecione uma pasta de instalação e uma pasta cache para o agente. O espaço livre de localização de cache deve ser pelo menos 5% dos dados de backup.
6. Se utilizar um servidor proxy para se ligar à internet, no ecrã de **configuração proxy,** introduza os detalhes do servidor proxy. Se utilizar um representante autenticado, introduza o nome de utilizador e os dados da palavra-passe neste ecrã.
7. O agente de backup Azure instala .NET Framework 4.5 e Windows PowerShell (se não estiverem instalados) para completar a instalação.
8. Depois de o agente ser instalado, **feche** a janela.

    ![Fechar](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registe o servidor DPM no cofre

1. Na consola do Administrador DPM > **Management,** clique **em Online**. Selecione **Registar**. Abrirá o Assistente do Servidor de Registos.
2. Na **Configuração proxy,** especifique as definições de procuração conforme necessário.

    ![Configuração do proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. No **Backup Vault,** navegue e selecione o ficheiro de credenciais de cofre que descarregou.

    ![Credenciais de cofre](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. Em **Definição de Estrangulamento,** pode ativar opcionalmente a largura de banda para obter backups. Pode definir os limites de velocidade para especificar horas e dias de trabalho.

    ![Definição de estrangulamento](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Na **definição da pasta de recuperação,** especifique um local que possa ser utilizado durante a recuperação de dados.

    - O Azure Backup utiliza esta localização como uma área de detenção temporária para dados recuperados.
    - Após a conclusão da recuperação de dados, o Azure Backup irá limpar os dados nesta área.
    - A localização deve ter espaço suficiente para guardar itens que se antecipa matem em paralelo.

    ![Definição de pasta de recuperação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. Na **definição de Encriptação,** gere ou forneça uma frase de passe.

    - A frase-passe é usada para encriptar as cópias de segurança à nuvem.
    - Especifique um mínimo de 16 caracteres.
    - Guarde o ficheiro num local seguro, é necessário para a recuperação.

    ![Encriptação](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > É dono da frase-passe de encriptação e a Microsoft não tem visibilidade.
    > Se a palavra-passe for perdida ou esquecida; A Microsoft não pode ajudar na recuperação dos dados de backup.

7. Clique em **Registar** para registar o servidor DPM no cofre.

Depois de o servidor ser registado com sucesso no cofre e agora está pronto para começar a fazer o backup do Microsoft Azure. Terá de configurar o grupo de proteção na consola DPM para fazer backup de cargas de trabalho para o Azure. [Aprenda](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) a implementar grupos de proteção.

## <a name="troubleshoot-vault-credentials"></a>Credenciais de cofre de resolução de problemas

### <a name="expiration-error"></a>Erro de expiração

O ficheiro de credenciais de cofre é válido apenas por 48 horas (depois de descarregado do portal). Se encontrar algum erro neste ecrã (por exemplo, "O ficheiro de credenciais de cofre fornecido expirou"), iniciar sessão no portal Azure e baixar novamente o ficheiro de credenciais do cofre.

### <a name="access-error"></a>Erro de acesso

Certifique-se de que o ficheiro de credenciais de cofre está disponível num local que pode ser acedido pela aplicação de configuração. Se encontrar erros relacionados com o acesso, copie o ficheiro de credenciais do cofre para um local temporário nesta máquina e tente novamente a operação.

### <a name="invalid-credentials-error"></a>Erro de credenciais inválidas

Se encontrar um erro credencial de cofre inválido (por exemplo, "Credenciais de cofre inválidas fornecidas") o ficheiro é corrompido ou não tem as credenciais mais recentes associadas ao serviço de recuperação.

- Tente novamente a operação depois de descarregar um novo ficheiro credencial de cofre do portal.
- Este erro é normalmente visto quando clica na opção de credencial do **cofre de descarregamento** no portal Azure, duas vezes em rápida sucessão. Neste caso, apenas o segundo ficheiro credencial do cofre é válido.
