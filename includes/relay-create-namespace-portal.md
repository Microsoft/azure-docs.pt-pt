1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel de navegação à esquerda do portal, clique em **+ Criar um recurso** e, em seguida, clique em **Integração do Enterprise** e em **Reencaminhamento**.
3. Na caixa de diálogo **Criar espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
4. No campo **Subscrição**, selecione a subscrição do Azure em que pretende criar o espaço de nomes.
5. No campo **[Grupo de recursos](../articles/azure-resource-manager/resource-group-portal.md)**, selecione um grupo de recursos existente em que o espaço de nomes será colocado ou crie um novo.      
6. Em **Localização**, selecione o país ou a região em que o espaço de nomes deverá ser alojado.
   
    ![Create namespace][create-namespace]
7. Clique em **Criar**. O sistema cria o espaço de nomes e ativa-o. Após alguns minutos, o sistema aprovisiona recursos para a sua conta.

### <a name="obtain-the-management-credentials"></a>Obter as credenciais de gestão

1. Clique em **Todos os recursos** e clique no nome do espaço de nomes criado recentemente.
2. Na janela de espaço de nomes Relay clique em **Políticas de acesso partilhado**.
3. Na janela **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. No ecrã **Política: RootManageSharedAccessKey**, clique no botão **Copiar**junto à **Cadeia de ligação – Chave primária** para copiar a cadeia de ligação para a sua área de transferência e utilizá-la mais tarde. Cole este valor no Bloco de Notas ou noutra localização temporária.
   
    ![connection-string][connection-string]

5. Repita o passo anterior, copie e cole o valor da **Chave primária** para uma localização temporária para utilizar mais tarde.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
