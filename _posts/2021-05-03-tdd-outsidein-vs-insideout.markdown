---
layout: post
title:  "TDD: Bottom-up vs Top-down"
date:   2021-05-03 01:38:27 +0300
categories: article
---
TDD is an approach used in industry in many ways and useful. In this post, two ways of TDD will be reviewed. First is named as insideout or bottom-up which starts development with lowest level components to highest.
Second is outsidein or top-down which starts with highest level components to lowest level. It is difficult to find a good example about TDD. Besides it is difficult to follow from video or article. I think all separate red and green phases can be followed. I want to present two examples for these two approaches.
As a testing suite Spring Test is used. There are lots of details of Spring Test, however it is out of this context. I will mention some critical points using Spring Test. 
I will show two examples in Springboot. Below is the diagram comparing two approaches. Development order is shown with arrows.

![topdown-bottomup](/assets/topdown-bottomup.png)
<br/>

<br><b>TDD: Short Introduction</b><br>
- Write test before production code
- First test must fail (RED)
- Write simplest code passing the test (GREEN)
- Refactor
- Don't write any test before passing existing ones

<br><b>Requirements Example</b><br>
- User can save a new employee 
- User can update an existing employee 
- User can get an employee by id
- User can get an employee by name
- User gets bad request for operations update and get, if given id or name does not exist


<br><b>First Approach: Bottom-up</b><br>

When Kent Beck first presented TDD, it was bottom-up. This approach is classissist way. There are no mocks or stubs. Starting with lowest components Red-Green-Refactor cycle runs.
I think this is more robust way since developer test highest level components such as controllers with exactly working components not with stubs. 
Another advantage is that several developers working on same code can reuse existing tests.

As shown below, red lines refer to the version which does not pass test, however green lines refer to the version passing test
Having committed all red and green phases in cycles, you can follow the code:
<a class="text-accent" href="https://github.com/gungor/tdd-insideout-example" >https://github.com/gungor/tdd-insideout-example</a><br>


<table>
	
	<tbody>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 1: Repository: search by name](https://github.com/gungor/tdd-insideout-example/commit/12ff61865dbc3596dc1f76507987558cdc9dc6fd)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 1: Repository: search by name](https://github.com/gungor/tdd-insideout-example/commit/b24460c10d8fa2831f07da26c7e9d9cddc65f21f)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 2: Service: save employee](https://github.com/gungor/tdd-insideout-example/commit/78f41445ab9dd2fd3402685b5c74739d39935ec9)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 2: Service: save employee](https://github.com/gungor/tdd-insideout-example/commit/da730f5bdae50b8cb1f50f98bfd20c8b0ecb11ab)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 3: Service: update employee](https://github.com/gungor/tdd-insideout-example/commit/f52e95c5b15eff504e592f1980769630e112b3b1)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 3: Service: update employee](https://github.com/gungor/tdd-insideout-example/commit/b9793976af527d42e7fd724002b8a41eada7e4ff)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 4: Service: update employee throws exception when id not exist](https://github.com/gungor/tdd-insideout-example/commit/d7c4833a87f465f26797155f99e489bdef7ea32f)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 4: Service: update employee throws exception when id not exist](https://github.com/gungor/tdd-insideout-example/commit/a6514eec42761f3d05dce2c60a80e8b1e80610a4)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 5: Service: get employee](https://github.com/gungor/tdd-insideout-example/commit/c6390f45389955a7dbd2cf1246e879d1ac2127ab)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 5: Service: get employee](https://github.com/gungor/tdd-insideout-example/commit/2dda5c36d4350158dfa2e6b3d54650d26bb472e9)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 6: Service: get employee throws exception when id not exist](https://github.com/gungor/tdd-insideout-example/commit/21999d13204bb9e5aaac26d3be7ddadbe4529397)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 6: Service: get employee throws exception when id not exist](https://github.com/gungor/tdd-insideout-example/commit/d3beb4d5699f74a457c75eddc8a96555c0861402)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 7: Service: get employee by name](https://github.com/gungor/tdd-insideout-example/commit/2166ecbfa72331db70ca3a7b36366545dbcfb642)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 7: Service: get employee by name](https://github.com/gungor/tdd-insideout-example/commit/264d3d6db0cc03dc6520dc745fa2d6eecc1cbc4a)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 8: Service: get employee by name throws exception when employee not found by name](https://github.com/gungor/tdd-insideout-example/commit/bd21b419d828fb3903f87ade6115edbfb1fc4b69)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 8: Service: get employee by name throws exception when employee not found by name](https://github.com/gungor/tdd-insideout-example/commit/460b7efd9e5660aa09c150295a85bfe986359a07)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 9: Controller: save employee](https://github.com/gungor/tdd-insideout-example/commit/b3702c7875a05095d14c3ca406836a8398ea3fb9)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 9: Controller: save employee](https://github.com/gungor/tdd-insideout-example/commit/d890794712bf171b56d39c1c32efc78e113f488e)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 10: Controller: update employee](https://github.com/gungor/tdd-insideout-example/commit/00ba1419dd20becdb8524162484b9a84dfc5518d)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 10: Controller: update employee](https://github.com/gungor/tdd-insideout-example/commit/56cbff6950065c8e45ee800e5c2bbfaf09adc0c2)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 11: Controller: get employee by id](https://github.com/gungor/tdd-insideout-example/commit/d489d07f9545c92c9eaa81d99c8de375ad8db30e)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 11: Controller: get employee by id](https://github.com/gungor/tdd-insideout-example/commit/30d7b6a1314e5b1bed253a0b1d3fbb7d36041d2d)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 12: Controller: get employee by name](https://github.com/gungor/tdd-insideout-example/commit/511306050fa1214a586e0bf4dd87084d3b39e082)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 12: Controller: get employee by name](https://github.com/gungor/tdd-insideout-example/commit/ba5d00458a089eeb1702809c59038b884744a7ca)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 13: Controller: return bad request when employee not found](https://github.com/gungor/tdd-insideout-example/commit/72bf93286a7d49169443c115994c3cbea16da610)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 13: Controller: return bad request when employee not found](https://github.com/gungor/tdd-insideout-example/commit/c75595cef272737ed57e24248abcdf88ea9b1aad)</td>
		</tr>
	</tbody>
</table>

<br><b>Second Approach: Top-down</b><br>
Mockist or outsidein or Top-down approach. It is believed to be started with this paper: <a class="text-accent" href="https://www2.ccs.neu.edu/research/demeter/related-work/extreme-programming/MockObjectsFinal.PDF" >Paper at XP</a><br>
Developer starts writing tests for highest level components first and mock dependent objects. That way there happens little test doubles contrary to classissist way therefore decreases refactoring cost.
Another advantage is on the integration side. This approach emphasizes integration of higher level components prior to development of lower components. Thus it allows to design your solution better than classisist way.

As shown below, red lines refer to the version which does not pass test, however green lines refer to the version passing test
Having committed all red and green phases in cycles, you can follow the code:
<a class="text-accent" href="https://github.com/gungor/tdd-outsidein-example" >https://github.com/gungor/tdd-outsidein-example</a><br>

<table>
	
	<tbody>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 1: Controller: save employee](https://github.com/gungor/tdd-outsidein-example/commit/d332e8124eae9dc2444a1662ff219283bdc75b14)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 1: Controller: save employee](https://github.com/gungor/tdd-outsidein-example/commit/b7f4e941bb5f6c2a547f2ba480623d10f5575a92)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 2: Controller: update employee](https://github.com/gungor/tdd-outsidein-example/commit/3b7f3f11e75f73e608f893be35ac57eb2c612726)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 2: Controller: update employee](https://github.com/gungor/tdd-outsidein-example/commit/547566e56975082c5e34441eaa72ba9c8f5c28a4)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 3: Controller: get employee by id](https://github.com/gungor/tdd-outsidein-example/commit/417292d24d4739ee544eea2a4f863cc14cd7f2ae)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 3: Controller: get employee by id](https://github.com/gungor/tdd-outsidein-example/commit/6b0d857b8b9bcc379ace97486c3c56f5a71a1171)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 4: Controller: get employee by name](https://github.com/gungor/tdd-outsidein-example/commit/5fdcea9e5b9044585a534b6e81c507a4c584a76f)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 4: Controller: get employee by name](https://github.com/gungor/tdd-outsidein-example/commit/2182532666c3bfa830817aa0038c39bf4a0899ee)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 5: Controller: return bad request when employee not found](https://github.com/gungor/tdd-outsidein-example/commit/3506432edc02023da7b0ead2aa2de46552cf769e)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 5: Controller: return bad request when employee not found](https://github.com/gungor/tdd-outsidein-example/commit/2d293f55637efcf86fd3071b994d322e5f2cb2a8)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 6: Service: save employee](https://github.com/gungor/tdd-outsidein-example/commit/20a246899df620962968ee85d3d4b8520b554012)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 6: Service: save employee](https://github.com/gungor/tdd-outsidein-example/commit/fbb9e47e035438dca74981310e647e0f63234c4e)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 7: Service: update employee](https://github.com/gungor/tdd-outsidein-example/commit/453a10bf54c8db615cc58d16fa20635ada0e1cad)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 7: Service: update employee](https://github.com/gungor/tdd-outsidein-example/commit/6a416a6018e4ad6f308f88da1f1e5a26ecca5889)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 8: Service: update employee throws exception when id not exist](https://github.com/gungor/tdd-outsidein-example/commit/90cea383c6deb26b7960cc8377b1d6600c9f6445)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 8: Service: update employee throws exception when id not exist](https://github.com/gungor/tdd-outsidein-example/commit/ed190a78f6a481aede3e225f7c85868f5886137b)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 9: Service: get employee](https://github.com/gungor/tdd-outsidein-example/commit/e054245957fc4c71d981e7aa476d87ae15953702)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 9: Service: get employee](https://github.com/gungor/tdd-outsidein-example/commit/f380c3365f7eb8da61b3039226d0aec5b5b50dc5)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 10: Service: get employee throws exception when id not exist](https://github.com/gungor/tdd-outsidein-example/commit/a1f0f7784afcfb6610e2ebae355aab948b6a7c3a)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 10: Service: get employee throws exception when id not exist](https://github.com/gungor/tdd-outsidein-example/commit/7bc6e585a460c0231dd5c6e8a72b843b7cc1b31f)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 11: Service: get employee by name](https://github.com/gungor/tdd-outsidein-example/commit/cfe883a4cae129af5bc61d647bf2cee7257f5620)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 11: Service: get employee by name](https://github.com/gungor/tdd-outsidein-example/commit/6272474eb14630b0cf28c02acecd8e00833b56ad)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 12: Service: get employee by name throws exception when employee not found by name](https://github.com/gungor/tdd-outsidein-example/commit/5e0438fbd75b834be5cc8b1e4991c56c2e8a0c5d)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 12: Service: get employee by name throws exception when employee not found by name](https://github.com/gungor/tdd-outsidein-example/commit/7522e1e31f4c1f5a81444f18d1e0cd81a417189f)</td>
		</tr>
		<tr>
			<td class="tdd-red" markdown="span">[Cycle 13: Repository: search by name](https://github.com/gungor/tdd-outsidein-example/commit/2fad6a2539718d3d33766a58dacd60fefe5cd587)</td>
		</tr>
		<tr>
			<td class="tdd-green" markdown="span">[Cycle 13: Repository: search by name](https://github.com/gungor/tdd-outsidein-example/commit/039d979c2a003f92ad4067825711c318a04efe62)</td>
		</tr>
		
	</tbody>
</table>

<br><b>Notes on Spring Test</b><br><br>
Do not use @Transactional on test methods. It may cause false negatives. There is a good <a class="text-accent" href="https://medium.com/@henrickkakutalua/dont-use-transactional-in-tests-65b857f63a4a" >article</a> about that 
<br><br>
Use TestEntityManager instead of Repository classes when retrieving records in database in order to assert. Since Hibernate uses first level cache, CrudRepository or JpaRepository may return entities from cache. Since TestEntityManager operations are transactional, it ensures that result received from TestEntityManager fetched from database not from first level cache.
<br><br>
Using TestEntityManager without @DataJpaTest requires transaction. You can use it inside TransactionTemplate.

<br><b>Notes on TDD</b><br><br>
Actually there is not a consensus on TDD. As you know there are classisists and mockists. There are also some authorities who does not think it brings benefit more than it costs. 
I think TDD can provide benefits in many cases but it costs time. Benefits of using TDD with legacy systems is also questionable. You may think about pros and cons of TDD before starting your project.

