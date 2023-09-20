import express from 'express'
import cors from 'cors'
import { initializeApp } from "firebase/app";
import { getFirestore, collection, getDocs, addDoc, deleteDoc, doc, getDoc, updateDoc} from "firebase/firestore";

const firebaseConfig = {
    apiKey: "AIzaSyDNeLeJWAi_OB4FL-GmRFhWJgKnfpIxSW0",
    authDomain: "proyecto-back-front-ee8a2.firebaseapp.com",
    projectId: "proyecto-back-front-ee8a2",
    storageBucket: "proyecto-back-front-ee8a2.appspot.com",
    messagingSenderId: "109472584320",
    appId: "1:109472584320:web:72fbf94ee8e50f7bb7cc65"
  }

  const firebase = initializeApp(firebaseConfig)
  const db = getFirestore()

//Settings de la aplicación
const app = express()
app.use(express.json())
app.use(cors())

//Creación de Rutas
app.get('/',async(req,res)=>{
    try{
        const Users = await collection(db, 'Users')
        const listUsers = await getDocs (Users)
        const aux = []
        listUsers.forEach((doc)=>{
            const obj ={
                id: doc.id,
                ...doc.data()
            }
            aux.push(obj)
        })
        res.send({
            'msg':'success',
            'data': aux
        })
    }catch(error){
        res.send({
            'msg':'error',
            'data': error
        })
    }
})

app.post('/create',async(req,res)=>{
    try{
    const body = req.body
    const Users = await collection(db, 'Users')
    await addDoc(Users, body)
        res.send({
            'msg':'success'
        })
    }catch(error){
        res.send({
            'msg':'error',
            'data': error
        })
    }
})

app.get('/delete/:id', async(req, res)=>{
    //console.log('@@@ param => ', req.params.id)
    const id = req.params.id
    try {
        await deleteDoc(doc(db, 'Users', id))    
        res.send({
            'msg': 'user deleted'
        })
    } catch (error) {
        res.send({
            'msg': 'error',
            'data': error
        })
    }
})

app.get('/get-update/:id', async (req, res) => {
    const id = req.params.id
    const userRef = doc(db, 'Users', id)
    const user = await getDoc(userRef)
    if(user.exists()) {
        res.send({
            'msg': 'success',
            'data': user.data()
        })
    }else {
        res.send({
            'msg': 'user doesnt exist'
        })
    }
})

app.post('/update', async(req,res)=>{
    const {id,firstname,lastname,address,city,phone,cp} = req.body
    const newData = {
        firstname,
        lastname,
        address,
        city,
        phone,
        cp
    }
    await updateDoc(doc(db, 'Users', id), newData)
    res.send({
        'msg': 'success'
    })
    //console.log('@@@ body => ', req.body)
})

//Prendemos el servidor
app.listen(9000, () => {
    console.log('Servidor trabajando')
})