flow: check type  
eslint  

JEST  
number  
toBe, toBeGreaterThanOrEqual  
string  
toMatch(/TEST/), toContain('TEST')  
array  
expect(["A","B"]).toEqual(expect.arrayContaining(["A","B"])) // expect the list contains A and B  
object  
toHaveProperty
expect({}).toEqual(expect.objectContaining({success:true})) // expect the response contains success:true

```
describe("number test",()=>{
  test("test name",()=>{
    expect(number).toBe(12)
  }
  test("test name2",()=>{
    expect(number).toBeGreaterThanOrEqual(12)
  }
})

```


npm run test -- --coverage 

node: supertest
```
const requst = require("supertest")
describe('test',()=>{
  beforeAll(async()=>{})
  afterAll(async()=>{})
  it("test user",()=>{
    const res = await request(app).get('/recipes')
    expect(res.statusCode).toEqual(200);
    expect(res.body).toEqual(expect.objectContaining({success:true,data:expect.any(Object)}))
.  })
})
```
jest.spyOn(RecipeService,'allRecipes').mockRejectedValueOnce(new error())

pre-push
