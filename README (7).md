import React, { useState } from 'react';
import {
  Text, View, TextInput, FlatList, TouchableOpacity, StyleSheet, SafeAreaView, StatusBar, Image
} from 'react-native';

export default function App() {
  const [tela, setTela] = useState('login');
  const [isDarkMode, setIsDarkMode] = useState(true);
  const [verSenha, setVerSenha] = useState(false);
  
  const [tarefas, setTarefas] = useState([
    { id: '1', titulo: 'Estudar React Native', descricao: 'Revisar componentes básicos e hooks.', status: 'concluída' },
    { id: '2', titulo: 'Trabalho de UI/UX', descricao: 'Finalizar protótipo de alta fidelidade.', status: 'pendente' },
    { id: '3', titulo: 'Projeto Database', descricao: 'Ajustar queries SQL do banco.', status: 'pendente' }
  ]);
  
  const [tarefaSelecionada, setTarefaSelecionada] = useState(null);
  const [novoTitulo, setNovoTitulo] = useState('');
  const [novaDescricao, setNovaDescricao] = useState('');
  const theme = {
    bg: isDarkMode ? '#000000' : '#F2F2F7',
    card: isDarkMode ? '#1C1C1E' : '#FFFFFF',
    text: isDarkMode ? '#FFFFFF' : '#000000',
    subtext: isDarkMode ? '#8E8E93' : '#636366',
    accent: '#007AFF',
    danger: '#FF3B30',
    success: '#34C759',
    input: isDarkMode ? '#2C2C2E' : '#E5E5EA'
  };
  const alternarStatus = (id) => {
    const novas = tarefas.map(t => t.id === id ? { ...t, status: t.status === 'concluída' ? 'pendente' : 'concluída' } : t);
    setTarefas(novas);
    if (tarefaSelecionada && tarefaSelecionada.id === id) {
      setTarefaSelecionada({ ...tarefaSelecionada, status: tarefaSelecionada.status === 'concluída' ? 'pendente' : 'concluída' });
    }
  };

  const adicionarTarefa = () => {
    if (novoTitulo.trim() === '') return;
    const nova = { id: Date.now().toString(), titulo: novoTitulo, descricao: novaDescricao, status: 'pendente' };
    setTarefas([nova, ...tarefas]);
    setNovoTitulo(''); setNovaDescricao('');
    setTela('home');
  };
  if (tela === 'login') {
    return (
      <View style={[styles.container, { backgroundColor: theme.bg }]}>
        <StatusBar barStyle={isDarkMode ? 'light-content' : 'dark-content'} />
        <View style={styles.headerCentral}>
          <Image 
            source={{ uri: 'https://cdn-icons-png.flaticon.com/512/9539/9539811.png' }} 
            style={styles.logoImage} 
          />
          <Text style={[styles.logo, { color: theme.text }]}>FazAí</Text>
          <Text style={[styles.subtitulo, { color: theme.subtext }]}>Gerenciador Acadêmico</Text>
        </View>
        <TextInput placeholder="Usuário" placeholderTextColor={theme.subtext} style={[styles.input, { backgroundColor: theme.input, color: theme.text }]} />
        <View style={styles.senhaWrapper}>
          <TextInput placeholder="Senha" secureTextEntry={!verSenha} placeholderTextColor={theme.subtext} style={[styles.inputSenha, { backgroundColor: theme.input, color: theme.text }]} />
          <TouchableOpacity onPress={() => setVerSenha(!verSenha)} style={styles.eyeBtn}>
            <Text style={{ color: theme.accent, fontWeight: 'bold', fontSize: 12 }}>{verSenha ? 'OCULTAR' : 'MOSTRAR'}</Text>
          </TouchableOpacity>
        </View>
        <TouchableOpacity style={[styles.btnMain, { backgroundColor: theme.accent }]} onPress={() => setTela('home')}>
          <Text style={styles.btnText}>Entrar</Text>
        </TouchableOpacity>
      </View>
    );
  }
  if (tela === 'home') {
    return (
      <SafeAreaView style={[styles.container, { backgroundColor: theme.bg }]}>
        <View style={styles.topBar}>
          <Text style={[styles.screenTitle, { color: theme.text }]}>Tarefas</Text>
          {/* Ajuste realizado na margem direita para trazer o botão mais para a esquerda */}
          <TouchableOpacity onPress={() => setTela('config')} style={styles.btnAjustes}>
            <Text style={{ color: theme.accent, fontSize: 16, fontWeight: '600' }}>Ajustes</Text>
          </TouchableOpacity>
        </View>
        <FlatList
          data={tarefas}
          keyExtractor={item => item.id}
          renderItem={({ item }) => (
            <TouchableOpacity 
              style={[styles.taskCard, { backgroundColor: theme.card }]} 
              onPress={() => { setTarefaSelecionada(item); setTela('detalhes'); }}
            >
              <TouchableOpacity onPress={() => alternarStatus(item.id)} style={[styles.checkCircle, { borderColor: item.status === 'concluída' ? theme.success : theme.subtext, backgroundColor: item.status === 'concluída' ? theme.success : 'transparent' }]} />
              <View style={{ flex: 1 }}>
                <Text style={[styles.taskTitle, { color: theme.text, textDecorationLine: item.status === 'concluída' ? 'line-through' : 'none' }]}>{item.titulo}</Text>
                <Text style={{ color: theme.subtext }} numberOfLines={1}>{item.descricao}</Text>
              </View>
            </TouchableOpacity>
          )}
        />
        <TouchableOpacity style={[styles.fab, { backgroundColor: theme.accent }]} onPress={() => setTela('adicionar')}>
          <Text style={styles.fabIcon}>+</Text>
        </TouchableOpacity>
      </SafeAreaView>
    );
  }
  if (tela === 'adicionar') {
    return (
      <View style={[styles.container, { backgroundColor: theme.bg }]}>
        <Text style={[styles.screenTitle, { color: theme.text, marginTop: 50 }]}>Nova Tarefa</Text>
        <TextInput placeholder="Título" value={novoTitulo} onChangeText={setNovoTitulo} placeholderTextColor={theme.subtext} style={[styles.input, { backgroundColor: theme.input, color: theme.text, marginTop: 20 }]} />
        <TextInput placeholder="Descrição..." value={novaDescricao} onChangeText={setNovaDescricao} multiline placeholderTextColor={theme.subtext} style={[styles.input, { backgroundColor: theme.input, color: theme.text, height: 150, textAlignVertical: 'top' }]} />
        <TouchableOpacity style={[styles.btnMain, { backgroundColor: theme.accent }]} onPress={adicionarTarefa}>
          <Text style={styles.btnText}>Salvar Tarefa</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => setTela('home')} style={styles.btnCancel}><Text style={{ color: theme.danger }}>Cancelar</Text></TouchableOpacity>
      </View>
    );
  }
  if (tela === 'detalhes') {
    return (
      <View style={[styles.container, { backgroundColor: theme.bg }]}>
        <Text style={[styles.screenTitle, { color: theme.text, marginTop: 50 }]}>Detalhes</Text>
        <View style={[styles.detailCard, { backgroundColor: theme.card }]}>
          <Text style={[styles.taskTitle, { color: theme.text, fontSize: 24 }]}>{tarefaSelecionada.titulo}</Text>
          <Text style={{ color: theme.subtext, fontSize: 16, marginTop: 15 }}>{tarefaSelecionada.descricao}</Text>
          <Text style={{ color: tarefaSelecionada.status === 'concluída' ? theme.success : theme.accent, fontWeight: '800', marginTop: 20 }}>
            STATUS: {tarefaSelecionada.status.toUpperCase()}
          </Text>
        </View>
        <TouchableOpacity style={[styles.btnMain, { backgroundColor: theme.accent }]} onPress={() => { alternarStatus(tarefaSelecionada.id); setTela('home'); }}>
          <Text style={styles.btnText}>Alterar Status</Text>
        </TouchableOpacity>
        <TouchableOpacity onPress={() => setTela('home')} style={styles.btnCancel}><Text style={{ color: theme.subtext }}>Voltar</Text></TouchableOpacity>
      </View>
    );
  }
  if (tela === 'config') {
    return (
      <View style={[styles.container, { backgroundColor: theme.bg }]}>
        <Text style={[styles.screenTitle, { color: theme.text, marginTop: 50 }]}>Ajustes</Text>
        <View style={[styles.settingRow, { backgroundColor: theme.card }]}>
          <Text style={{ color: theme.text, fontSize: 18 }}>
            {isDarkMode ? 'Modo Escuro' : 'Modo Claro'}
          </Text>
          <TouchableOpacity 
            style={[styles.toggle, { backgroundColor: isDarkMode ? theme.success : theme.subtext }]} 
            onPress={() => setIsDarkMode(!isDarkMode)}
          >
            <View style={[styles.toggleBall, { alignSelf: isDarkMode ? 'flex-end' : 'flex-start' }]} />
          </TouchableOpacity>
        </View>
        <TouchableOpacity style={[styles.btnMain, { backgroundColor: theme.accent, marginTop: 30 }]} onPress={() => setTela('home')}>
          <Text style={styles.btnText}>Concluído</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.btnLogout} onPress={() => setTela('login')}>
          <Text style={{ color: theme.danger, fontWeight: 'bold' }}>Sair da Conta</Text>
        </TouchableOpacity>
      </View>
    );
  }

  return null;
}

const styles = StyleSheet.create({
  container: { flex: 1, padding: 25 },
  headerCentral: { alignItems: 'center', marginTop: 70, marginBottom: 40 },
  logoImage: { width: 110, height: 110, marginBottom: 15 },
  logo: { fontSize: 42, fontWeight: '900' },
  subtitulo: { fontSize: 16 },
  input: { padding: 18, borderRadius: 15, marginBottom: 15, fontSize: 16 },
  senhaWrapper: { flexDirection: 'row', alignItems: 'center', marginBottom: 15 },
  inputSenha: { flex: 1, padding: 18, borderRadius: 15, fontSize: 16 },
  eyeBtn: { position: 'absolute', right: 15, padding: 5 },
  btnMain: { padding: 20, borderRadius: 15, alignItems: 'center', marginTop: 10 },
  btnText: { color: '#fff', fontWeight: 'bold', fontSize: 18 },
  btnCancel: { marginTop: 20, alignItems: 'center' },
  topBar: { flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center', marginTop: 40, marginBottom: 20 },
  screenTitle: { fontSize: 32, fontWeight: '800' },
  btnAjustes: { marginRight: 20 }, // Margem aumentada para afastar da borda direita
  taskCard: { flexDirection: 'row', padding: 20, borderRadius: 20, marginBottom: 12, alignItems: 'center' },
  checkCircle: { width: 24, height: 24, borderRadius: 12, borderWidth: 2, marginRight: 15 },
  taskTitle: { fontSize: 18, fontWeight: '700' },
  fab: { position: 'absolute', bottom: 30, right: 30, width: 65, height: 65, borderRadius: 32.5, justifyContent: 'center', alignItems: 'center', elevation: 5 },
  fabIcon: { color: '#fff', fontSize: 35, fontWeight: '300' },
  detailCard: { padding: 25, borderRadius: 25, marginVertical: 20 },
  settingRow: { flexDirection: 'row', justifyContent: 'space-between', padding: 20, borderRadius: 20, marginTop: 20, alignItems: 'center' },
  toggle: { width: 55, height: 30, borderRadius: 15, padding: 4 },
  toggleBall: { width: 22, height: 22, backgroundColor: '#fff', borderRadius: 11 },
  btnLogout: { marginTop: 40, alignItems: 'center' }
});
