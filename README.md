// App atualizado com ordenação por data, notas, rolês, edição e remoção de tarefas
import React, { useState, useEffect } from 'react';

const tarefasPadrao = [
  "Lavar roupa",
  "Preparar almoço",
  "Preparar jantar",
  "Passear com Lady",
  "Limpar a casa",
  "Lavar o banheiro 1",
  "Lavar o banheiro 2",
  "Lavar louça",
  "Fazer compras",
  "Outra"
];

const App = () => {
  const [loggedIn, setLoggedIn] = useState(false);
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [currentUser, setCurrentUser] = useState(null);
  const [users, setUsers] = useState({ tylerxerecudo: 'xe01' });
  const [newUser, setNewUser] = useState({ username: '', password: '' });
  const [taskList, setTaskList] = useState([]);
  const [newTask, setNewTask] = useState({ date: '', time: '', task: '', person: '', other: '' });
  const [tab, setTab] = useState('tarefas');
  const [now, setNow] = useState(new Date());
  const [rolês, setRoles] = useState([]);
  const [newRole, setNewRole] = useState({ date: '', time: '', location: '', people: '' });
  const [notes, setNotes] = useState('');
  const [editIndex, setEditIndex] = useState(null);

  useEffect(() => {
    const timer = setInterval(() => setNow(new Date()), 60000);
    return () => clearInterval(timer);
  }, []);

  const handleLogin = () => {
    if (users[username] === password) {
      setLoggedIn(true);
      setCurrentUser(username);
    } else {
      alert('Usuário ou senha inválidos');
    }
  };

  const logout = () => {
    setLoggedIn(false);
    setCurrentUser(null);
    setUsername('');
    setPassword('');
  };

  const addUser = () => {
    if (newUser.username && newUser.password) {
      setUsers({ ...users, [newUser.username]: newUser.password });
      setNewUser({ username: '', password: '' });
      alert('Novo usuário criado!');
    }
  };

  const addTask = () => {
    const taskName = newTask.task === 'Outra' ? newTask.other : newTask.task;
    if (taskName && newTask.person && newTask.date && newTask.time) {
      const updatedList = [...taskList];
      if (editIndex !== null) {
        updatedList[editIndex] = { ...newTask, task: taskName };
        setEditIndex(null);
      } else {
        updatedList.push({ ...newTask, task: taskName });
      }
      setTaskList(updatedList);
      setNewTask({ date: '', time: '', task: '', person: '', other: '' });
    }
  };

  const deleteTask = (index) => {
    const updated = [...taskList];
    updated.splice(index, 1);
    setTaskList(updated);
  };

  const editTask = (index) => {
    const t = taskList[index];
    setNewTask({ ...t, other: t.task });
    setEditIndex(index);
    setTab('novaTarefa');
  };

  const addRole = () => {
    if (newRole.date && newRole.time && newRole.location && newRole.people) {
      setRoles([...rolês, newRole]);
      setNewRole({ date: '', time: '', location: '', people: '' });
    }
  };

  const tarefasOrdenadas = [...taskList].sort((a, b) => new Date(`${a.date}T${a.time}`) - new Date(`${b.date}T${b.time}`));
  const tarefasAtrasadas = tarefasOrdenadas.filter(t => new Date(`${t.date}T${t.time}`) < now);

  if (!loggedIn) {
    return (
      <div style={{ padding: '2rem', textAlign: 'center' }}>
        <h1>BEM VINDOS, XERECUDOS!</h1>
        <img src="https://images.app.goo.gl/7Fyt7oxrsMqPJqX8A" alt="Boas-vindas" style={{ maxWidth: '300px', margin: '1rem auto' }} />
        <input type="text" placeholder="Usuário" value={username} onChange={e => setUsername(e.target.value)} style={{ display: 'block', margin: '0.5rem auto' }} />
        <input type="password" placeholder="Senha" value={password} onChange={e => setPassword(e.target.value)} style={{ display: 'block', margin: '0.5rem auto' }} />
        <button onClick={handleLogin}>Entrar</button>
      </div>
    );
  }

  return (
    <div style={{ padding: '2rem' }}>
      <h1>🏠 Bem-vindos, Xerecudos!</h1>
      <p>Logado como: <strong>{currentUser}</strong></p>
      <button onClick={logout} style={{ float: 'right' }}>Sair</button>

      {tarefasAtrasadas.length > 0 && (
        <div style={{ backgroundColor: '#ffd6d6', padding: '1rem', margin: '1rem 0', border: '1px solid red' }}>
          <strong>⚠️ Tarefas atrasadas:</strong>
          <ul>
            {tarefasAtrasadas.map((t, i) => (
              <li key={i}>{t.task} por {t.person} em {t.date} às {t.time}</li>
            ))}
          </ul>
        </div>
      )}

      <h2>📋 Tarefas Registradas</h2>
      <table border="1" cellPadding="8" style={{ borderCollapse: 'collapse', width: '100%' }}>
        <thead>
          <tr>
            <th>Data</th>
            <th>Horário</th>
            <th>Tarefa</th>
            <th>Responsável</th>
            <th>Ações</th>
          </tr>
        </thead>
        <tbody>
          {tarefasOrdenadas.map((t, i) => {
            const isAtrasada = new Date(`${t.date}T${t.time}`) < now;
            return (
              <tr key={i} style={{ backgroundColor: isAtrasada ? '#ffe6e6' : 'white' }}>
                <td>{t.date}</td>
                <td>{t.time}</td>
                <td>{t.task}</td>
                <td>{t.person}</td>
                <td>
                  <button onClick={() => editTask(i)}>✏️</button>
                  <button onClick={() => deleteTask(i)}>🗑️</button>
                </td>
              </tr>
            );
          })}
        </tbody>
      </table>

      <div style={{ marginTop: '2rem' }}>
        <button onClick={() => setTab('novaTarefa')}>➕ Registrar Nova Tarefa</button>
        <button onClick={() => setTab('rolê')}>🎉 Rolêzinho</button>
        {currentUser === 'tylerxerecudo' && (
          <button onClick={() => setTab('usuarios')} style={{ marginLeft: '1rem' }}>👤 Registros de Usuários</button>
        )}
      </div>

      {tab === 'novaTarefa' && (
        <div style={{ marginTop: '2rem' }}>
          <h3>{editIndex !== null ? 'Editar Tarefa' : 'Adicionar Tarefa'}</h3>
          <input type="date" value={newTask.date} onChange={e => setNewTask({ ...newTask, date: e.target.value })} />
          <input type="time" value={newTask.time} onChange={e => setNewTask({ ...newTask, time: e.target.value })} />
          <select value={newTask.task} onChange={e => setNewTask({ ...newTask, task: e.target.value })}>
            <option value="">-- Selecione a tarefa --</option>
            {tarefasPadrao.map((t, i) => (
              <option key={i} value={t}>{t}</option>
            ))}
          </select>
          {newTask.task === 'Outra' && (
            <input placeholder="Escreva a tarefa" value={newTask.other} onChange={e => setNewTask({ ...newTask, other: e.target.value })} />
          )}
          <input placeholder="Responsável" value={newTask.person} onChange={e => setNewTask({ ...newTask, person: e.target.value })} />
          <button onClick={addTask}>{editIndex !== null ? 'Salvar' : 'Adicionar'}</button>
          <button onClick={() => { setTab('tarefas'); setEditIndex(null); }}>Voltar</button>
        </div>
      )}

      {tab === 'rolê' && (
        <div style={{ marginTop: '2rem' }}>
          <h3>🎉 Agenda de Rolês</h3>
          <input type="date" value={newRole.date} onChange={e => setNewRole({ ...newRole, date: e.target.value })} />
          <input type="time" value={newRole.time} onChange={e => setNewRole({ ...newRole, time: e.target.value })} />
          <input placeholder="Local" value={newRole.location} onChange={e => setNewRole({ ...newRole, location: e.target.value })} />
          <input placeholder="Quem vai" value={newRole.people} onChange={e => setNewRole({ ...newRole, people: e.target.value })} />
          <button onClick={addRole}>Agendar</button>
          <button onClick={() => setTab('tarefas')}>Voltar</button>
          <ul>
            {rolês.map((r, i) => (
              <li key={i}>{r.date} às {r.time} - {r.people} no {r.location}</li>
            ))}
          </ul>
        </div>
      )}

      {tab === 'usuarios' && currentUser === 'tylerxerecudo' && (
        <div style={{ marginTop: '2rem' }}>
          <h3>Registrar Novo Usuário</h3>
          <input placeholder="Novo usuário" value={newUser.username} onChange={e => setNewUser({ ...newUser, username: e.target.value })} />
          <input placeholder="Senha" type="password" value={newUser.password} onChange={e => setNewUser({ ...newUser, password: e.target.value })} />
          <button onClick={addUser}>Registrar</button>
          <button onClick={() => setTab('tarefas')}>Voltar</button>
        </div>
      )}

      {/* Notas */}
      <div style={{ marginTop: '2rem', backgroundColor: '#fff9c4', padding: '1rem', borderRadius: '8px', border: '1px dashed #ccc' }}>
        <h3>📌 Notas</h3>
        <textarea value={notes} onChange={e => setNotes(e.target.value)} placeholder="Escreva lembretes ou observações..." rows="4" style={{ width: '100%' }} />
      </div>
    </div>
  );
};

export default App;
