### Documentation.md

# Employee Management System - Documentation

## Overview
Este documento fornece uma explicação detalhada do código do projeto **Employee Management System**, incluindo as tecnologias envolvidas, o passo a passo para realizar este projeto e comentários linha por linha do código para facilitar a compreensão de pessoas leigas.

## Tecnologias Utilizadas
- **React**: Biblioteca JavaScript para construir interfaces de usuário.
- **TypeScript**: Superset do JavaScript que adiciona tipagem estática.
- **React Hook Form**: Biblioteca para gerenciar formulários em React.
- **Zod**: Biblioteca para validação de esquemas e dados.
- **Axios**: Cliente HTTP baseado em promessas para fazer requisições.
- **Styled Components**: Biblioteca para estilização de componentes em React.
- **React Router**: Biblioteca para roteamento em aplicações React.

## Passo a Passo para Realizar o Projeto
1. **Configurar o ambiente de desenvolvimento**: Instale Node.js e configure o projeto com Create React App.
2. **Instalar dependências**: Instale as bibliotecas necessárias, como React, TypeScript, Axios, React Hook Form, Zod e Styled Components.
3. **Criar a estrutura do projeto**: Organize o projeto em componentes, páginas e serviços.
4. **Desenvolver componentes**: Crie componentes reutilizáveis e estilize-os usando Styled Components.
5. **Implementar lógica de negócio**: Use React Hook Form e Zod para gerenciar e validar formulários.
6. **Configurar Axios para chamadas de API**: Configure Axios para lidar com requisições HTTP.
7. **Gerenciar estado e efeitos colaterais**: Utilize hooks do React como useState e useEffect para gerenciar estado e efeitos colaterais.
8. **Testar e depurar**: Teste a aplicação para garantir que tudo funcione corretamente.

## Explicação do Código

### Header Component
```typescript
import React, { useState } from 'react'; // Importa React e useState hook
import { HeaderContainer, Logo, UserGreeting, AddNewButton, BodyContainer, GeneralContainer, UserContainer, ExitIcon, LabelTitle, UserIcon } from './headerhomescreenstyles'; // Importa estilos personalizados
import PopupLogout from '../popuplogout/popuplogout'; // Importa o componente de logout

// Define as propriedades do componente Header
interface HeaderProps {
  onAddNew: () => void; // Função chamada ao adicionar um novo funcionário
}

// Define o componente Header como uma função de componente de React
const Header: React.FC<HeaderProps> = ({ onAddNew }) => {
  const [showLogout, setShowLogout] = useState(false); // Define um estado para mostrar o popup de logout

  return (
    <GeneralContainer>
      <HeaderContainer>
        <Logo><span>front-end-</span>emcash</Logo> {/* Logo do sistema */}
        <UserContainer>
          <UserIcon src="/assets/homescreen/usercircle.svg"/> {/* Ícone do usuário */}
          <UserGreeting>Olá, EmCasher</UserGreeting> {/* Saudação ao usuário */}
          <ExitIcon src="/assets/homescreen/exiticonhomescreen.svg" onClick={() => setShowLogout(true)} /> {/* Ícone de logout */}
        </UserContainer>
      </HeaderContainer>
      <BodyContainer>
        <LabelTitle>Lista de Funcionários</LabelTitle> {/* Título da seção */}
        <AddNewButton onClick={onAddNew}>Adicionar novo</AddNewButton> {/* Botão para adicionar novo funcionário */}
      </BodyContainer>
      {showLogout && <PopupLogout onClose={() => setShowLogout(false)} />} {/* Popup de logout */}
    </GeneralContainer>
  );
};

export default Header; // Exporta o componente Header
```

### Table Component
```typescript
import React, { useEffect, useState } from 'react'; // Importa React, useEffect e useState hooks
import api from '../../../services/api/api'; // Importa o serviço de API
import { useForm, SubmitHandler } from 'react-hook-form'; // Importa React Hook Form
import { z } from 'zod'; // Importa Zod para validação de esquemas
import { zodResolver } from '@hookform/resolvers/zod'; // Importa o resolver Zod para React Hook Form
import { TableContainer, TableRow, TableHeader, TableCell, EditButton, DeleteButton, EditIconTable, DeleteIconTable, BoxIcon, EyeOpenIcon, TableHeaderNome, TableCellNome, TableHeaderBox, TableHeaderEdit, TableHeaderDelete, TableCellBox, TableCellButton } from './homescreentablestyles'; // Importa estilos personalizados

// Define a interface Employee para representar os dados dos funcionários
interface Employee {
  id: string;
  nome: string;
  cpf: string;
  celular: string;
  email: string;
}

// Define as propriedades do componente Table
interface TableProps {
  onEdit: (employee: Employee) => void; // Função chamada ao editar um funcionário
  onDelete: (employee: Employee) => void; // Função chamada ao excluir um funcionário
}

// Define o componente Table como uma função de componente de React
const Table: React.FC<TableProps> = ({ onEdit, onDelete }) => {
  const [employees, setEmployees] = useState<Employee[]>([]); // Define um estado para armazenar os funcionários

  useEffect(() => {
    const fetchEmployees = async () => {
      const token = localStorage.getItem("emcash_token"); // Obtém o token do localStorage
      try {
        const response = await api.get('/funcionarios', {
          headers:{
            "Authorization": `Bearer ${token}` // Define o cabeçalho de autorização
          }
        });
        setEmployees(response.data); // Define os funcionários no estado
        console.log(response);
      } catch (error) {
        console.error('Error fetching employees', error); // Exibe erros no console
      }
    };

    fetchEmployees(); // Chama a função para buscar os funcionários
  }, []);

  return (
    <TableContainer>
      <thead>
        <TableRow>
          <TableHeaderBox><BoxIcon src="/assets/homescreen/iconboxtoselect.svg" /></TableHeaderBox> {/* Caixa de seleção */}
          <TableHeaderNome>Nome completo</TableHeaderNome> {/* Cabeçalho da coluna de nome */}
          <TableHeader>CPF/CNPJ<EyeOpenIcon src="/assets/homescreen/iconeyeon.svg"/></TableHeader> {/* Cabeçalho da coluna de CPF */}
          <TableHeader>Celular</TableHeader> {/* Cabeçalho da coluna de celular */}
          <TableHeader>E-mail</TableHeader> {/* Cabeçalho da coluna de e-mail */}
          <TableHeaderEdit>Editar</TableHeaderEdit> {/* Cabeçalho da coluna de edição */}
          <TableHeaderDelete>Apagar</TableHeaderDelete> {/* Cabeçalho da coluna de exclusão */}
        </TableRow>
      </thead>
      <tbody>
        {employees.map((employee) => (
          <TableRow key={employee.id}>
            <TableCellBox><BoxIcon src="/assets/homescreen/iconboxtoselect.svg" /></TableCellBox> {/* Caixa de seleção */}
            <TableCellNome>{employee.nome}</TableCellNome> {/* Nome do funcionário */}
            <TableCell>{employee.cpf}</TableCell> {/* CPF do funcionário */}
            <TableCell>{employee.celular}</TableCell> {/* Celular do funcionário */}
            <TableCell>{employee.email}</TableCell> {/* E-mail do funcionário */}
            <TableCellButton>
              <EditButton onClick={() => onEdit(employee)}>
                <EditIconTable src="/assets/homescreen/editiconhomescreen.svg" /> {/* Botão de edição */}
              </EditButton>
            </TableCellButton>
            <TableCellButton>
              <DeleteButton onClick={() => onDelete(employee)}>
                <DeleteIconTable src="/assets/homescreen/deleteiconhomescreen.svg" /> {/* Botão de exclusão */}
              </DeleteButton>
            </TableCellButton>
          </TableRow>
        ))}
      </tbody>
    </TableContainer>
  );
};

export default Table; // Exporta o componente Table
```

### ModalAddNew Component
```typescript
import React, { useState } from 'react'; // Importa React e useState hook
import api from '../../../services/api/api'; // Importa o serviço de API
import { useForm, SubmitHandler } from 'react-hook-form'; // Importa React Hook Form
import { z } from 'zod'; // Importa Zod para validação de esquemas
import { zodResolver } from '@hookform/resolvers/zod'; // Importa o resolver Zod para React Hook Form
import { ModalContainer, ModalContent, ModalHeader, ModalBody, ModalFooter, Input, Button, ButtonCancel, ErrorMessage } from './modaladdnewstyles'; // Importa estilos personalizados

// Define o esquema de validação usando Zod
const schema = z.object({
  nome: z.string().min(3, "Este campo é obrigatório"),
  cpf: z.string().min(11, "Um CPF valido deve conter no minimo 11 caracteres"),
  celular: z.string().min(10, "Um numero válido conter no minimo 10 caracteres"),
  email: z.string().email("Formato de e-mail inválido"),
});

// Define o tipo dos dados do formulário com base no esquema de validação
type FormData = z.infer<typeof schema>;

// Define o componente ModalAddNew como uma função de componente de React
const ModalAddNew:

 React.FC<{ onClose: () => void }> = ({ onClose }) => {
  const { register, handleSubmit, formState: { errors } } = useForm<FormData>({
    resolver: zodResolver(schema), // Usa o resolver Zod para validação
  });

  const onSubmit: SubmitHandler<FormData> = async (data) => {
    const token = localStorage.getItem("emcash_token"); // Obtém o token do localStorage
    try {
      await api.post('/funcionario/cadastro', data, {
        headers: {
          "Authorization": `Bearer ${token}`, // Define o cabeçalho de autorização
        },
      });
      onClose(); // Fecha o modal
      window.location.reload(); // Recarrega a página para refletir a mudança
    } catch (error) {
      console.error('Error adding employee', error); // Exibe erros no console
    }
  };

  return (
    <ModalContainer>
      <ModalContent>
        <ModalHeader>Adicionar Novo Funcionário</ModalHeader> {/* Cabeçalho do modal */}
        <ModalBody>
          <Input type="text" placeholder="Nome completo" {...register('nome')} /> {/* Campo de entrada para nome */}
          {errors.nome && <ErrorMessage>{errors.nome.message}</ErrorMessage>} {/* Mensagem de erro para nome */}
          <Input type="text" placeholder="CPF/CNPJ" {...register('cpf')} /> {/* Campo de entrada para CPF */}
          {errors.cpf && <ErrorMessage>{errors.cpf.message}</ErrorMessage>} {/* Mensagem de erro para CPF */}
          <Input type="text" placeholder="Celular" {...register('celular')} /> {/* Campo de entrada para celular */}
          {errors.celular && <ErrorMessage>{errors.celular.message}</ErrorMessage>} {/* Mensagem de erro para celular */}
          <Input type="email" placeholder="E-mail" {...register('email')} /> {/* Campo de entrada para e-mail */}
          {errors.email && <ErrorMessage>{errors.email.message}</ErrorMessage>} {/* Mensagem de erro para e-mail */}
        </ModalBody>
        <ModalFooter>
          <ButtonCancel onClick={onClose}>Cancelar</ButtonCancel> {/* Botão para cancelar */}
          <Button onClick={handleSubmit(onSubmit)}>Adicionar</Button> {/* Botão para adicionar */}
        </ModalFooter>
      </ModalContent>
    </ModalContainer>
  );
};

export default ModalAddNew; // Exporta o componente ModalAddNew
```

### ModalEdit Component
```typescript
import React from 'react'; // Importa React
import { useForm, SubmitHandler } from 'react-hook-form'; // Importa React Hook Form
import { z } from 'zod'; // Importa Zod para validação de esquemas
import { zodResolver } from '@hookform/resolvers/zod'; // Importa o resolver Zod para React Hook Form
import api from '../../../services/api/api'; // Importa o serviço de API
import { ModalContainer, ModalContent, ModalHeader, ModalBody, ModalFooter, FormGroup, Label, Input, Button, ButtonCancel, ErrorMessage } from './modaleditstyles'; // Importa estilos personalizados

// Define o esquema de validação usando Zod
const schema = z.object({
  nome: z.string().min(3, "Este campo é obrigatório"),
  cpf: z.string().min(11, "Um CPF válido deve conter no mínimo 11 caracteres"),
  celular: z.string().min(10, "Um número válido deve conter no mínimo 10 caracteres"),
  email: z.string().email("Formato de e-mail inválido"),
});

// Define o tipo dos dados do formulário com base no esquema de validação
type FormData = z.infer<typeof schema>;

// Define o componente ModalEdit como uma função de componente de React
const ModalEdit: React.FC<{ onClose: () => void, employee: any }> = ({ onClose, employee }) => {
  const { register, handleSubmit, formState: { errors }, reset } = useForm<FormData>({
    resolver: zodResolver(schema), // Usa o resolver Zod para validação
    defaultValues: employee, // Define os valores padrão do formulário
  });

  const onSubmit: SubmitHandler<FormData> = async (data) => {
    const token = localStorage.getItem("emcash_token"); // Obtém o token do localStorage
    try {
      await api.patch(`/funcionario/${employee.id}`, data, {
        headers: {
          "Authorization": `Bearer ${token}`, // Define o cabeçalho de autorização
        },
      });
      onClose(); // Fecha o modal
      window.location.reload(); // Recarrega a página para refletir a mudança
    } catch (error) {
      console.error('Error updating employee', error); // Exibe erros no console
    }
  };

  return (
    <ModalContainer>
      <ModalContent>
        <ModalHeader>Editar Funcionário</ModalHeader> {/* Cabeçalho do modal */}
        <ModalBody>
          <FormGroup>
            <Input type="text" id="nome" {...register('nome')} placeholder=" " /> {/* Campo de entrada para nome */}
            <Label htmlFor="nome">Nome completo</Label> {/* Rótulo para nome */}
            {errors.nome && <ErrorMessage>{errors.nome.message}</ErrorMessage>} {/* Mensagem de erro para nome */}
          </FormGroup>

          <FormGroup>
            <Input type="text" id="cpf" {...register('cpf')} placeholder=" " /> {/* Campo de entrada para CPF */}
            <Label htmlFor="cpf">CPF/CNPJ</Label> {/* Rótulo para CPF */}
            {errors.cpf && <ErrorMessage>{errors.cpf.message}</ErrorMessage>} {/* Mensagem de erro para CPF */}
          </FormGroup>

          <FormGroup>
            <Input type="text" id="celular" {...register('celular')} placeholder=" " /> {/* Campo de entrada para celular */}
            <Label htmlFor="celular">Celular</Label> {/* Rótulo para celular */}
            {errors.celular && <ErrorMessage>{errors.celular.message}</ErrorMessage>} {/* Mensagem de erro para celular */}
          </FormGroup>

          <FormGroup>
            <Input type="email" id="email" {...register('email')} placeholder=" " /> {/* Campo de entrada para e-mail */}
            <Label htmlFor="email">E-mail</Label> {/* Rótulo para e-mail */}
            {errors.email && <ErrorMessage>{errors.email.message}</ErrorMessage>} {/* Mensagem de erro para e-mail */}
          </FormGroup>
        </ModalBody>
        <ModalFooter>
          <ButtonCancel className="cancel" onClick={onClose}>Cancelar</ButtonCancel> {/* Botão para cancelar */}
          <Button className="save" onClick={handleSubmit(onSubmit)}>Salvar Alterações</Button> {/* Botão para salvar alterações */}
        </ModalFooter>
      </ModalContent>
    </ModalContainer>
  );
};

export default ModalEdit; // Exporta o componente ModalEdit
```

### PopupDelete Component
```typescript
import React from 'react'; // Importa React
import { PopupContainer, PopupContent, PopupHeader, PopupBody, PopupFooter, Button, ButtonCancel } from './popupdeletestyles'; // Importa estilos personalizados
import api from '../../../services/api/api'; // Importa o serviço de API
import { AxiosError } from 'axios'; // Importa AxiosError

// Define a interface Employee para representar os dados dos funcionários
interface Employee {
  id: string;
  nome: string;
  cpf: string;
  celular: string;
  email: string;
}

// Define as propriedades do componente PopupDelete
interface PopupDeleteProps {
  onClose: () => void; // Função chamada ao fechar o popup
  employee: Employee; // Dados do funcionário a ser deletado
}

// Define o componente PopupDelete como uma função de componente de React
const PopupDelete: React.FC<PopupDeleteProps> = ({ onClose, employee }) => {
  const handleDelete = async () => {
    const token = localStorage.getItem("emcash_token"); // Obtém o token do localStorage
    if (!token) {
      console.error("Token not found in localStorage"); // Exibe erro no console
      return;
    }
    if (employee) {
      try {
        await api.delete(`/funcionario/${employee.id}`, {
          headers: {
            "Authorization": `Bearer ${token}` // Define o cabeçalho de autorização
          }
        });
        onClose(); // Fecha o popup
        window.location.reload(); // Recarrega a página para refletir a mudança
      } catch (err: unknown) {
        const error = err as AxiosError;
        console.error('Error deleting employee', error); // Exibe erros no console
        if (error.response) {
          console.error('Server response:', error.response.data); // Exibe resposta do servidor no console
        } else {
          console.error('Error message:', error.message); // Exibe mensagem de erro no console
        }
      }
    }
  };

  return (
    <PopupContainer>
      <PopupContent>
        <PopupHeader>Apagar Funcionário</PopupHeader> {/* Cabeçalho do popup */}
        <PopupBody>Esta ação é irreversível. Tem certeza que deseja apagar este funcionário do sistema?</PopupBody> {/* Corpo do popup */}
        <PopupFooter>
          <ButtonCancel onClick={onClose}>Cancelar</ButtonCancel> {/* Botão para cancelar */}
          <Button onClick={handleDelete}>Apagar</Button> {/* Botão para apagar */}
        </PopupFooter>
      </PopupContent>
    </Popup>
