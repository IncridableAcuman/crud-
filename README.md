performing crud operations through nodejs in mongodb

const mongoose=require('mongoose');
const express=require('express');
const categoriesRoute=require('./route/categories');
const Joi=require('joi');
//const customersRoute=require('./customersRoute/customers');
const app=express();
app.use(express.json());
app.use('/api/categories',categoriesRoute);
//app.use('/api/customers',customersRoute);

mongoose.connect('mongodb://localhost/virtualdars')
.then(()=>{
    console.log('MongoDBga ulanish hosil qilindi...')
})
.catch((err)=>{
    console.log('mongoDBga ulanish paytida nosozlik yuz berdi!')
});
const port=process.env.PORT || 5000;
app.listen(port,()=>{
    console.log(`Server is listening on ${port}`);
});
const c = require('config');
const express=require('express');
const Joi = require('joi');
const router=express.Router();
const Joi=require('joi');
const mongoose=require('mongoose');

const customersSchema=new mongoose.Schema({
    name:{
        type:String,
        minlength:5,
        maxlength:50,
        required:true
    },
    isVip:{
        type:Boolean,
        default:false
    },
    phone:{
        type:String,
        minlength:5,
        maxlength:50
    }
});
const Customers= mongoose.model('Customers',customersSchema);

router.get('/',async (req,res)=>{
    let customers=await Customers.find({isVip:true});
    res.send(customers);
});
router.post('/', async (req,res)=>{
    let {error}=validationCustomers(req.body);
    if(error)
       return res.status(400).send('error');

    let customer=new Customers({
        name:req.body.name,
        isVip:req.body.isVip,
        phone:req.body.phone
    });
     customer= await customer.save();
    res.status(201).send(customers);
});
router.get('/:id',async (req,res)=>{
    let customers=await Customers.findById(req.params.id);
    if(!customers)
        return res.status(404).send('not found');

    res.send(customers);
});
router.put('/:id',async(req,res)=>{
const {error}=validationCustomers(req.body);
if(error)
    res.status(400).send('errror');
let customer=await Customers.findByIdAndUpdate(req.params.id,{isVip:req.body.isVip},{
    new:true
});
if(!customer)
    res.status(404).send('not found');

res.send(customer);
});

router.delete('/:id',async (req,res)=>{
    let customer=await Customers.findByIdAndDelete(req.params.id);
    if(!customer)
        res.status(404).send('not found');

    res.send(customer);

})
function validationCustomers(customers){
const schema={
name:Joi.string().min(5).max(50).required(),
isVip:Joi.boolean.required(),
phone:Joi.string().min(5).max(50).required()
}
return Joi.validate(customers,schema);
}
module.exports=router;
