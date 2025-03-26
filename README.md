
# Remover Grupo de Distribuição Dinâmica no Exchange On-Premises com Sincronização para o Office 365

Este guia fornece os passos para remover um **grupo de distribuição dinâmica** em um ambiente **Exchange On-Premises** sincronizado com o **Office 365** (Azure AD), usando o **Exchange Management Shell** e o **PowerShell** do Exchange Online.

## Passos para Remover o Grupo de Distribuição Dinâmica

### 1. Acessar o Exchange Management Shell no Servidor On-Premises
Abra o **Exchange Management Shell** no servidor Exchange local. **Não use o PowerShell comum.**

### 2. Remover o Grupo de Distribuição Dinâmica no Exchange On-Premises
Para remover o grupo de distribuição dinâmica no Exchange On-Premises, execute o seguinte comando:

```powershell
Remove-DynamicDistributionGroup -Identity "NomeDoGrupo"
```

Substitua **"NomeDoGrupo"** pelo nome do seu grupo de distribuição dinâmica.

### 3. Confirmar a Remoção
Após executar o comando, você será solicitado a confirmar a remoção do grupo. Digite `Y` para confirmar.

### 4. Verificar se o Grupo foi Removido no Exchange On-Premises
Após a remoção, verifique se o grupo foi realmente removido com o seguinte comando:

```powershell
Get-DynamicDistributionGroup -Identity "NomeDoGrupo"
```

Se o grupo foi removido corretamente, **não será retornado nenhum resultado**.

---

## Passos Adicionais para Remover o Grupo na Nuvem (Exchange Online)

Como seu **AD local** está sincronizado com o **Office 365**, a remoção do grupo de distribuição dinâmica também pode ser necessária no Exchange Online (na nuvem).

### 1. Conectar ao Exchange Online via PowerShell
Se você ainda não estiver conectado ao Exchange Online, execute os seguintes comandos para se conectar:

```powershell
$UserCredential = Get-Credential
$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Authentication Basic -AllowRedirection -Credential $UserCredential
Import-PSSession $Session -DisableNameChecking
```

### 2. Remover o Grupo de Distribuição Dinâmica no Exchange Online
Agora, para remover o grupo de distribuição dinâmica na nuvem, execute o seguinte comando:

```powershell
Remove-DynamicDistributionGroup -Identity "NomeDoGrupo"
```

**Importante:** Mesmo que o grupo tenha sido removido no Exchange On-Premises, ele ainda pode existir no Exchange Online, devido à sincronização do Azure AD. Portanto, é necessário remover o grupo também na nuvem.

### 3. Confirmar a Remoção no Exchange Online
Após executar o comando de remoção no Exchange Online, verifique se o grupo foi removido corretamente com o seguinte comando:

```powershell
Get-DynamicDistributionGroup -Identity "NomeDoGrupo"
```

Se o grupo não for retornado, a remoção foi bem-sucedida na nuvem.

---

## Troubleshooting

### 1. **Erro: Não foi possível encontrar o grupo**
Se você receber a mensagem **"Não foi possível encontrar o grupo"**, certifique-se de que o nome do grupo está correto. Verifique a grafia e, se necessário, use o comando abaixo para listar todos os grupos de distribuição dinâmica no Exchange On-Premises:

```powershell
Get-DynamicDistributionGroup
```

Se o grupo estiver sincronizado com o Exchange Online, você pode precisar verificar a nuvem também com:

```powershell
Get-DynamicDistributionGroup -Identity "NomeDoGrupo" -IncludeSoftDeletedRecipients
```

### 2. **Erro: O grupo está em uso**
Se o grupo estiver em uso por outras configurações (como filtros de email ou associações), você poderá receber um erro informando que não é possível removê-lo. Para resolver isso:

- Verifique as dependências do grupo com o comando:

  ```powershell
  Get-DynamicDistributionGroup
  ```

- Certifique-se de que não há filtros ou configurações em uso pelo grupo. Se houver, remova a associação antes de tentar removê-lo.

### 3. **Erro: Permissões Insuficientes**
Se você não tem permissões suficientes para remover o grupo, certifique-se de que sua conta possui permissões administrativas tanto no Exchange On-Premises quanto no Exchange Online. Caso contrário, entre em contato com o administrador do Exchange para garantir que você tenha as permissões necessárias.

### 4. **Erro: "O grupo é protegido contra exclusão"**
Se você receber a mensagem **"O grupo é protegido contra exclusão"**, a proteção de exclusão pode estar habilitada para o grupo. Para remover a proteção, execute o seguinte comando:

```powershell
Set-DynamicDistributionGroup -Identity "NomeDoGrupo" -ProtectRemoval $false
```

Após isso, tente remover o grupo novamente.

---

## Referências

- [Documentação Oficial do Exchange On-Premises](https://docs.microsoft.com/pt-br/exchange/)
- [Comando Remove-DynamicDistributionGroup](https://docs.microsoft.com/pt-br/powershell/module/exchange/remove-dynamicdistributiongroup)
- [Documentação do Exchange Online](https://docs.microsoft.com/pt-br/exchange/exchange-online)

---

**Observação:** Este guia é válido para **Exchange On-Premises** sincronizado com **Exchange Online**. Se você não tiver a sincronização ou se estiver utilizando apenas o **Exchange Online**, os comandos podem ser diferentes.
