### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparar para uma instalação de push num computador Windows

1. Certifique-se de que existe conectividade de rede entre o computador do Windows e o servidor de processos.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ter direitos de administrador local ou domínio. Utilize esta conta apenas para a instalação de push e para as atualizações de agente.

   > [!NOTE]
   > Se não utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local. Para desativar o controlo de acesso de utilizador remoto, na chave de registo HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicionar um novo DWORD: **LocalAccountTokenFilterPolicy**. Defina o valor como **1**. Para executar esta tarefa uma linha de comandos, execute o seguinte comando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
3. Na Firewall do Windows no computador que pretende proteger, selecione **permitir que uma aplicação ou funcionalidade através da Firewall**. Ativar **impressora partilha de ficheiros e** e **Windows Management Instrumentation (WMI)**. Para computadores que pertençam a um domínio, pode configurar as definições de firewall utilizando um objeto de política de grupo (GPO).

   ![Definições de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

4. Adicione a conta que criou no CSPSConfigtool. Siga estes passos.

    a. Inicie sessão no servidor de configuração.

    b. Abra **cspsconfigtool.exe**. Está disponível como um atalho no ambiente de trabalho e na pasta %ProgramData%\home\svsystems\bin.

    c. No **gerir contas** separador, selecione **adicionar conta**.

    d. Adicione a conta que criou.

    e. Introduza as credenciais que utiliza para ativar a replicação para um computador.
