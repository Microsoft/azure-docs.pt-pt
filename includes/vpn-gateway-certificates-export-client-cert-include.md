Gerar um certificado de cliente, é instalado automaticamente no computador que utilizou para gerá-lo. Se pretender instalar o certificado de cliente no outro computador cliente, terá de exportar o certificado de cliente que gerou.

1. Para exportar um certificado de cliente, abra **Gerir Certificados de Utilizador**. Os certificados de cliente que gerou são, por predefinição, localizados em 'Certificados - User\Personal\Certificates atual'. O certificado de cliente que pretende exportar, clique com o botão direito **todas as tarefas**e, em seguida, clique em **exportar** para abrir o **Assistente para exportar certificados**.

  ![Exportar](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. No Assistente para exportar certificados, clique em **seguinte** para continuar.

  ![Seguinte](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Selecione **Sim, exportar a chave privada**e, em seguida, clique em **seguinte**.

  ![Exportar chave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Na página **Exportar Formato de Ficheiro**, deixe as predefinições selecionadas. Certifique-se de que **Incluir todos os certificados no caminho de certificação, se possível** está selecionado. Esta definição exporta, além disso, as informações do certificado de raiz é necessárias para a autenticação de cliente com êxito. Sem ela, autenticação de cliente falha porque o cliente não tem o certificado de raiz fidedigna. Clique depois em **Seguinte**.

  ![formato de ficheiro de exportação](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Na página **Segurança**, tem de proteger a chave privada. Se optar por utilizar uma palavra-passe, certifique-se de que regista ou memoriza a palavra-passe que define para este certificado. Clique depois em **Seguinte**.

  ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Em **Ficheiro a Exportar**, **Navegue** até à localização para a qual pretende exportar o certificado. Em **Nome do ficheiro**, atribua um nome ao ficheiro de certificado. Clique depois em **Seguinte**.

  ![ficheiro a exportar](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Clique em **Concluir** para exportar o certificado.

  ![terminar](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)